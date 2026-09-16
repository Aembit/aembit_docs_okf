---
type: how-to
title: "Self-host the MCP Identity Gateway"
description: "Deploy and operate the Aembit MCP Identity Gateway on your own Linux host."
resource: https://docs.aembit.io/user-guide/deploy-install/mcp-identity-gateway/self-host-mcp-gateway/
interface: mcp
tags: ["mcp-identity-gateway", "deploy-install"]
timestamp: 2026-09-15T18:18:13-07:00
---

# Self-host the MCP Identity Gateway

This guide explains how to deploy and operate the

Model Context Protocol (MCP) Identity Gateway on your own Linux host, as a standalone service that you run and maintain.

> **Most teams should use the managed service**
>
> Aembit operates the MCP Identity Gateway as a managed service, and that’s the recommended path for most teams. The managed service handles provisioning, TLS termination, certificate renewal, and runtime operations for you. See [Set up the MCP Identity Gateway](../../access-policies/mcp-identity-gateway/setup-mcp-gateway.md) to use it.
>
> Self-host the Gateway only when you must run it in your own infrastructure. For example, you might need to keep MCP traffic inside a network boundary you control, meet data-locality requirements, or integrate with existing host-level tooling.

When you self-host, you run the MCP Identity Gateway as a `systemd` service on a Linux host that you provision and maintain, alongside a colocated [Agent Controller](../about-agent-controller.md). A bare virtual machine and a cloud instance (such as an AWS EC2 instance) follow the same procedure—an EC2 instance *is* a virtual machine for this purpose.

## How self-hosting differs from the managed service

The Aembit Cloud configuration is identical for both deployment models: you create an Identity Provider, two Access Policies, and a Credential Provider for each MCP server. Self-hosting adds the host-side work that Aembit otherwise does for you:

| Responsibility                                                          | Managed service                   | Self-host                                                   |
| ----------------------------------------------------------------------- | --------------------------------- | ----------------------------------------------------------- |
| Compute host                                                            | Aembit                            | You provision and maintain a Linux host                     |
| Gateway hostname                                                        | `<tenantId>.mcpgateway.aembit.io` | A DNS name you own (for example, `mcp-gateway.example.com`) |
| TLS certificate                                                         | Aembit                            | You obtain and renew it                                     |
| Gateway runtime and upgrades                                            | Aembit                            | You install, start, and upgrade the binary                  |
| Aembit Cloud configuration (IdP, Access Policies, Credential Providers) | You                               | You                                                         |

This guide covers the host-side work. Once the Gateway is running, you [configure Aembit Cloud](#configure-aembit-cloud) using the existing setup guide.

## Prerequisites

Before you begin, ensure you have:

* An Aembit Tenant with admin access

* A Linux host (a virtual machine or cloud instance) for the MCP Identity Gateway, with network access to:

  * Your Aembit Tenant (`https://<tenantId>.aembit.io`, outbound HTTPS)
  * Target MCP servers (outbound HTTPS)
  * MCP clients and AI agents (inbound on the listener port, typically 443)

* A DNS name you control for the Gateway (for example, `mcp-gateway.example.com`)

* The ability to obtain a TLS certificate for that DNS name (a public certificate authority or your internal public key infrastructure)

## Provision the host

Create a Linux host for the MCP Identity Gateway. Aembit supports the Gateway as a `systemd` service on a virtual machine or cloud instance; an Ubuntu host is a common choice.

Size the host’s network access so it can:

* Accept inbound MCP client connections (typically on port 443)
* Reach the Aembit control plane (outbound HTTPS)
* Reach every MCP server you plan to proxy (outbound HTTPS)

For detailed host setup guidance, see [Linux virtual machine deployment](../virtual-machine/overview.md).

> **EC2 security groups**
>
> On AWS, open inbound **443** (HTTPS) so MCP clients can reach the Gateway. If you use Let’s Encrypt for TLS (described later), also open inbound **80** (HTTP) *temporarily* for the certificate challenge, then close it again once you have the certificate.

## Install Agent Controller

The MCP Identity Gateway requires a colocated Agent Controller on the same host. The Agent Controller registers the Gateway with Aembit Cloud and supplies it with the credentials and configuration it needs to operate. Because the two run on the same host and communicate over localhost, credentials never traverse the network.

Create the Agent Controller in your Aembit Tenant, then install it on the Gateway host before you install the Gateway itself. For full instructions, see [Install Agent Controller on Linux](../virtual-machine/linux/agent-controller-install-linux.md). Use the following configuration:

| Setting      | Value                                                                                          | Reason                                                                                   |
| ------------ | ---------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| TLS          | Off                                                                                            | The MCP Identity Gateway terminates TLS and reaches the Agent Controller over localhost. |
| Registration | [Trust Provider-based](../about-agent-controller.md#during-registration) | Recommended for automated registration                                                   |
| Port         | 5000 (default)                                                                                 | The MCP Identity Gateway connects to `http://localhost:5000`                             |

## Set up DNS

Point your chosen DNS name at the host so MCP clients can reach the Gateway.

Create an address (A) record that maps your Gateway hostname (for example, `mcp-gateway.example.com`) to the host’s public IP address. On AWS, you create this record in Route 53 for the hosted zone of the domain you control.

Confirm the record resolves before continuing:

```shell
dig +short mcp-gateway.example.com
```

Expected: the host’s public IP address.

## Obtain a TLS certificate

The MCP Identity Gateway terminates TLS itself, so it needs a certificate and private key for your Gateway hostname.

The following steps use [Let’s Encrypt](https://letsencrypt.org/) with `certbot` to issue a certificate. This is a quick option for a single host; for production you might instead offload TLS to a load balancer or use your internal public key infrastructure.

1. Open inbound port **80** on the host (and in your cloud security group) so Let’s Encrypt can complete the HTTP challenge.

2. Install `nginx`, which `certbot` uses to answer the challenge:

   ```shell
   sudo apt update
   sudo apt install nginx -y
   sudo systemctl enable nginx
   ```

3. Install `certbot` and its `nginx` plugin:

   ```shell
   sudo apt install -y certbot python3-certbot-nginx
   ```

4. Request a certificate for your Gateway hostname (replace `mcp-gateway.example.com` with your own):

   ```shell
   sudo certbot --nginx -d mcp-gateway.example.com
   ```

   `certbot` writes the certificate chain and private key to `/etc/letsencrypt/live/mcp-gateway.example.com/`.

5. Stop and disable `nginx` so it doesn’t compete with the Gateway for port 443:

   ```shell
   sudo systemctl stop nginx
   sudo systemctl disable nginx
   ```

6. Close inbound port **80** again—the Gateway only needs inbound 443 for normal operation.

> **Certificate renewal**
>
> Let’s Encrypt certificates expire after 90 days. Because `nginx` is stopped, schedule renewal with a method that doesn’t need port 80 held open continuously (for example, `certbot renew` with the standalone plugin during a brief maintenance window), and reopen port 80 only for the duration of the renewal.

## Install and start the Gateway

Download, install, and start the MCP Identity Gateway on the host. On startup, the Gateway registers with Aembit Cloud through the colocated Agent Controller.

1. Download the MCP Identity Gateway archive from the [Aembit releases portal](https://releases.aembit.io/mcp_gateway/) and extract it:

   ```shell
   tar xf <archive-name>.tar.gz
   ```

2. Install the Gateway, supplying its configuration as environment variables on the install command:

   > **Keep configuration out of shell history**
   >
   > The following command shows configuration values inline for clarity. In production, load them from an environment file with restricted permissions (`chmod 600`) or a secrets manager so they don’t appear in shell history or process listings.

   ```shell
   sudo AEMBIT_AUTHORIZATION_SERVER=https://<tenantId>.mcp.useast2.aembit.io/ \
        AEMBIT_MCP_GATEWAY_URL=https://mcp-gateway.example.com \
        AEMBIT_TLS_CERT_CHAIN_PATH=/etc/letsencrypt/live/mcp-gateway.example.com/fullchain.pem \
        AEMBIT_TLS_PRIVATE_KEY_PATH=/etc/letsencrypt/live/mcp-gateway.example.com/privkey.pem \
        AEMBIT_AGENT_CONTROLLER_URL=http://localhost:5000 \
        AEMBIT_LOG_LEVEL=info \
        ./install
   ```

   Replace `<tenantId>` with your Aembit Tenant ID (visible in your Aembit Tenant URL—for example, `abc123` in `https://abc123.aembit.io`), and replace `mcp-gateway.example.com` with your own Gateway hostname. For a full description of every variable, see [MCP Identity Gateway environment variables](env-vars-mcp-gateway.md).

## Verify the Gateway service

Confirm the Gateway is running before you configure Aembit Cloud.

1. Check that the service is active:

   ```shell
   sudo systemctl status aembit_mcp_gateway
   ```

   Expected: `active (running)`. If it isn’t, check the [environment variables](env-vars-mcp-gateway.md) and the TLS certificate paths.

2. Confirm the endpoint is reachable and presents a valid certificate:

   ```shell
   curl -I "https://mcp-gateway.example.com/"
   ```

   Expected: the TLS handshake succeeds and you get an HTTP response (possibly an error, since this isn’t a valid MCP request). If it fails, confirm DNS resolves and the certificate is valid for the hostname.

3. Review the Gateway logs for startup or connection events:

   ```shell
   sudo journalctl --namespace aembit_mcp_gateway -n 50
   ```

## Allow MCP client traffic through your WAF

If you put a Web Application Firewall (WAF) or reverse proxy in front of the Gateway, its default rules can block legitimate MCP clients before their requests reach the Gateway.

MCP clients differ in how they construct HTTP requests, and new clients continue to appear. Aembit can’t anticipate every client’s behavior, so treat WAF tuning as an ongoing part of operating a self-hosted Gateway:

* **Monitor your WAF logs** for blocked requests, and adjust your rules as the MCP clients you support change their behavior.
* **Exclude rules that reject requests with a missing `User-Agent` header.** Some MCP clients, including Microsoft Copilot, send requests without one.

New MCP clients can exhibit unexpected behavior as AI systems evolve. Some leading MCP clients, for example, send requests with no `User-Agent` header, and common managed rule sets may reject them outright. On AWS WAF, the `NoUserAgent_HEADER` rule in `AWSManagedRulesCommonRuleSet` would block every request to the Gateway for those MCP clients, including the OAuth discovery endpoints and `/mcp`.

The symptoms point away from your firewall. The client’s sign-in window can open blank and report an error such as this one:

`Failed to login. Could not discover authorization server metadata.`

Meanwhile, your Aembit Tenant records no Access Authorization or Workload events, because the requests never reach the Gateway. Override the missing-`User-Agent` rule in your managed rule set, then retry the connection. Confirm the result in your firewall logs rather than the Gateway logs—a blocked request leaves no trace on the Gateway.

> **This applies only to self-hosted Gateways**
>
> Aembit’s managed MCP Identity Gateway already accounts for this rule. You only need to tune these rules when you self-host the Gateway behind a WAF you operate.

## Configure Aembit Cloud

With the Gateway running, complete the Aembit Cloud configuration—the Identity Provider, both Access Policies, and a Credential Provider for each MCP server. These steps are identical to the managed service.

Follow [Set up the MCP Identity Gateway](../../access-policies/mcp-identity-gateway/setup-mcp-gateway.md), starting at [Configure an Identity Provider](../../access-policies/mcp-identity-gateway/setup-mcp-gateway.md#configure-an-identity-provider), with one substitution:

> **Use your own Gateway hostname**
>
> Wherever the setup guide references the managed endpoint `<tenantId>.mcpgateway.aembit.io`, use your own Gateway hostname instead (for example, `mcp-gateway.example.com`). This applies to the **Server Workload** **Host** and the **Credential Provider** **Audience** in the client-to-Gateway Access Policy.

After you configure both Access Policies, return to the setup guide’s [Verify the connection](../../access-policies/mcp-identity-gateway/setup-mcp-gateway.md#verify-the-connection) section to test the end-to-end flow.

## Operate the MCP Identity Gateway

Because you run the Gateway yourself, you also operate it:

* **Restart the service** after a configuration change:

  ```shell
  sudo systemctl restart aembit_mcp_gateway
  ```

  A restart ends every open MCP session unless you configured a Valkey session store. See [Session persistence](session-persistence-mcp-gateway.md).

* **Follow the logs** while troubleshooting:

  ```shell
  sudo journalctl --namespace aembit_mcp_gateway -f
  ```

* **Upgrade** by downloading a newer release and rerunning the install command.

* **Renew TLS certificates** before they expire, as described in [Obtain a TLS certificate](#obtain-a-tls-certificate).

For connectivity, log, and metrics details, see the [MCP Identity Gateway reference](reference-mcp-gateway.md).

## Next steps

* [MCP Identity Gateway environment variables](env-vars-mcp-gateway.md) - Every variable the Gateway and its Agent Controller accept at install time
* [Session persistence](session-persistence-mcp-gateway.md) - How to keep MCP sessions across restarts with Valkey
* [MCP Identity Gateway reference](reference-mcp-gateway.md) - Token formats, proxied MCP methods, connectivity, and workload events
* [Client workload identification](../../access-policies/mcp-identity-gateway/client-workload-identification.md) - How the Gateway identifies users in multi-user deployments
