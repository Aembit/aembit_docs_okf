---
type: how-to
title: "Kerberos Trust Provider"
description: "How to configure a Kerberos Trust Provider"
resource: https://docs.aembit.io/user-guide/access-policies/trust-providers/kerberos-trust-provider/
interface: web-ui
tags: ["trust-provider", "access-policy"]
timestamp: 2026-09-15T20:39:46-07:00
---

# Kerberos Trust Provider

The Kerberos Trust Provider enables the attestation of Client Workloads running on virtual machines (VMs) joined to Active Directory (AD).

This attestation method serves on-premises deployments where alternative attestation methods, such as the AWS or Azure metadata service Trust Providers, aren’t available.

This Trust Provider is unique because it relies on attestation provided by an Aembit component, rather than attestation from a third-party system. In this scenario, the Agent Controller acts as the attesting system. Agent Proxy authenticates to the Agent Controller with a Kerberos ticket. The Agent Controller signs the authenticated identity into an attested document, and Agent Proxy sends that document to Aembit Cloud with each request. Aembit Cloud verifies the signature and applies the Trust Provider’s match rules as part of the Access Policy evaluation.

## Prerequisites

Kerberos based attestation is available only for [Virtual Machine Deployments](../../deploy-install/virtual-machine/overview.md).

### Join your Edge Components to the AD domain

* You must join Agent Controller VMs to AD before you install Agent Controller on them.

* You must join Client Workload VMs to AD before you install Agent Proxy on them.

### Service principal name for the Agent Controller

Agent Proxy requests a Kerberos ticket for the Service Principal Name (SPN) `HTTP/<hostname>`, where `<hostname>` is the host part of the `AEMBIT_AGENT_CONTROLLER` value you give the Agent Proxy installer. The AD account that the Agent Controller service runs as must hold that SPN. You choose the account, and its name doesn’t matter to Aembit. The account needs no specific permissions in AD.

Which account holds the SPN depends on your deployment:

* **Single Agent Controller on Windows Server** - The host’s computer account holds the SPN. The default SPN registrations Windows creates when the host joins the domain already cover `HTTP/<hostname>`. Register `HTTP/<name>` explicitly only when Agent Proxies reach the host by a different name.

* **Single Agent Controller on Linux** - An AD account you choose holds the SPN. Register `HTTP/<hostname>` on it and export a keytab for the Agent Controller VM.

* **Multiple Agent Controllers behind a load balancer** - One account shared by every Agent Controller in the cluster holds the SPN. On Windows Server, use a Group Managed Service Account (gMSA). Register `HTTP/<load balancer hostname>` on it. See [High availability](#high-availability).

### Agent Controller service account on Windows Server

By default, the Agent Controller service on Windows Server runs as `LocalService`, which accepts inbound Kerberos authentication as the host’s computer account. You don’t need to set `SERVICE_LOGON_ACCOUNT` for a single Agent Controller.

For a high availability (HA) configuration, set `SERVICE_LOGON_ACCOUNT` to the gMSA in [Down-Level Logon Name format](https://learn.microsoft.com/en-us/windows/win32/secauthn/user-name-formats#down-level-logon-name), for example `SERVICE_LOGON_ACCOUNT=<NetBIOS domain name>\<sAMAccountName of gMSA>$`. The installer accepts a built-in account or a gMSA. It can’t run the service as a domain user that requires a password.

### Network access

* Agent Controller VMs don’t need access to the Domain Controller to validate Agent Proxy tickets. Windows Server does need the Domain Controller to retrieve a gMSA password, and whoever creates or rotates a Linux keytab needs it too.

* Client Workload VMs must have access to the Domain Controller to acquire tickets.

### Linux Agent Proxy host requirements

Kerberos attestation on Linux shells out to the host’s Kerberos and HTTP client tools. The Agent Proxy installer checks for `curl`, `kinit`, `klist`, `sg`, and `visudo`, and the install fails when any of them is missing. Install your distribution’s MIT Kerberos client package to get `kinit` and `klist`.

### Keytabs

* Agent Controller on Linux

  * Agent Controller Linux VMs require a keytab file containing the key for the SPN.

  * You can place the keytab file on the VM before or after the Agent Controller installation.

  * Set `KRB5_KTNAME` to the keytab path when you install. When you don’t set it, Kerberos uses `/etc/krb5.keytab`.

  * The Agent Controller service runs as the Linux user `aembit_agent_controller`, which must have read permission on the keytab file. If you place a keytab file before you install the Agent Controller, create a Linux group `aembit` and a Linux user `aembit_agent_controller` first, and make the file readable by that user or group.

  * If your organization has mandatory AD password rotation, make sure you have a configuration in place for keytab renewal.

    See [Agent Controller keytab rotation on Linux](#agent-controller-keytab-rotation-on-linux) for more information.

* Agent Controller on Windows Server

  * No keytab. The service uses the credentials of the account it runs as.

* Agent Proxy on Linux

  * The Agent Proxy on the Client Workload VM uses the host keytab file, `/etc/krb5.keytab` by default.

  * By default, the Agent Proxy authenticates as the computer account principal from the host keytab, whose [sAMAccountName](https://learn.microsoft.com/en-us/windows/win32/ad/naming-properties#samaccountname) is the computer name followed by `$`. To authenticate as a different principal, set `AEMBIT_AGENT_PROXY_KERBEROS_PRINCIPAL`.

  * The host keytab can keep Linux `root:root` ownership. Set `AEMBIT_PRIVILEGED_KEYTAB=true` and the installer grants the Agent Proxy read access to it.

* Agent Proxy on Windows Server

  * No keytab. The Agent Proxy service runs as `NetworkService` and authenticates as the host’s computer account.

## Agent Controller registration

An Agent Controller can’t use the Kerberos Trust Provider for its own registration with Aembit Cloud. The Agent Controller is the component that signs Kerberos attestations, so it can’t attest itself the same way.

Register Agent Controllers that run on domain-joined VMs on premises with a [Device Code](../../deploy-install/about-agent-controller.md#during-registration). An Agent Controller VM in AWS, Azure, or Kubernetes can instead register with an AWS Role, AWS Metadata Service, Azure Metadata Service, or Kubernetes Service Account Trust Provider. These are the only Trust Provider types an Agent Controller can register with.

## Kerberos Trust Provider match rules

The Kerberos Trust Provider supports three match rule attributes. The **Attribute** dropdown in the Aembit Tenant UI lists them as `principal`, `realm / domain`, and `sourceIp`.

> **Important**
>
> When matching on `principal` or `realm / domain`, see [Kerberos principal formatting](#kerberos-principal-formatting) for guidance.

| Attribute        | Description                                    | Example                                      |
| ---------------- | ---------------------------------------------- | -------------------------------------------- |
| `principal`      | The principal Agent Proxy authenticated as     | `webapp01$@EXAMPLE.COM`, `EXAMPLE\webapp01$` |
| `realm / domain` | The realm or NetBIOS domain from the principal | `EXAMPLE.COM`, `EXAMPLE`                     |
| `sourceIp`       | The source IP address of the Agent Proxy       | `192.168.1.100`                              |

Aembit Cloud evaluates match rules as follows:

* `principal` and `sourceIp` values are case-sensitive. `realm / domain` values are case-insensitive.

* Every value accepts `*` as a wildcard, and it matches the whole attested value rather than part of it. For example, `*@EXAMPLE.COM` matches any principal in the `EXAMPLE.COM` realm, and `EXAMPLE\webapp*` matches the computer accounts whose names start with `webapp`. A value with no `*` must match the whole attested value.

* Computer account principals end in `$`. Include the `$` in the value, or end the value with `*`.

* Rules on the same attribute match when any one of them matches. Rules on different attributes must all match.

* When a rule doesn’t match, the [access authorization event](../../audit-report/access-authorization-events.md) records what Aembit attested in its `actualValue` field. Compare that value against your rule to find the difference.

* The Agent Controller signs an attested document that’s valid for one hour, and Agent Proxy reuses it until it expires. Aembit Cloud rejects an expired document.

### Agent Controller field

During the configuration of the Kerberos Trust Provider, you must select at least one Agent Controller in the **Agent Controller** field. Aembit Cloud rejects an attested document signed by an Agent Controller the Trust Provider doesn’t list, before it checks the signature or the match rules.

### Kerberos principal formatting

Windows and Linux format the authenticated principal differently. Linux treats AD purely as Kerberos, and Windows treats it natively as AD.

The following table details all the combinations you can encounter based on the OS installed on Agent Controller and Agent Proxy:

| Agent Controller | Agent Proxy | Principal format                          |
| ---------------- | ----------- | ----------------------------------------- |
| Linux            | Linux       | `<sAMAccount name>@<realm>`               |
| Linux            | Windows     | `<User name>@<realm>`                     |
| Windows          | Linux       | `<NetBIOS domain name>\<sAMAccount name>` |
| Windows          | Windows     | `<NetBIOS domain name>\<User name>`       |

For example, a Linux Agent Controller produces `webapp01$@EXAMPLE.COM`; a Windows Agent Controller produces `EXAMPLE\webapp01$`.

Aembit Cloud parses either the realm or the NetBIOS domain from the attested principal, so a `realm / domain` rule works with both formats.

## Enable Kerberos attestation

By default, Aembit disables Kerberos attestation on both Agent Controller and Agent Proxy.

Follow the applicable sections to enable Kerberos attestation on Aembit Edge Components:

### Agent Controller on Windows Server

To enable Kerberos attestation for [Agent Controller on a Windows Server VM](../../deploy-install/virtual-machine/windows/agent-controller-install-windows.md), set the following installer property:

```shell
AEMBIT_KERBEROS_ATTESTATION_ENABLED=true
```

For an HA configuration with a gMSA, also set `SERVICE_LOGON_ACCOUNT=<NetBIOS domain name>\<sAMAccountName of gMSA>$`.

### Agent Controller on Linux

To enable Kerberos attestation for [Agent Controller on a Linux VM](../../deploy-install/virtual-machine/linux/agent-controller-install-linux.md), set the following environment variables:

```shell
AEMBIT_KERBEROS_ATTESTATION_ENABLED=true
KRB5_KTNAME=<Agent Controller keytab file location>
```

When you don’t set `KRB5_KTNAME`, Kerberos uses `/etc/krb5.keytab`, which is root-only on most hosts. The `aembit_agent_controller` Linux user must be able to read whichever keytab the Agent Controller uses.

### Agent Proxy on Linux

To enable Kerberos attestation for [Agent Proxy on a Linux VM](../../deploy-install/virtual-machine/linux/agent-proxy-install-linux.md), set `AEMBIT_KERBEROS_ATTESTATION_ENABLED` and one of the two principal options alongside the standard installation variables.

To authenticate as the computer account from a root-only host keytab:

```shell
AEMBIT_KERBEROS_ATTESTATION_ENABLED=true
AEMBIT_PRIVILEGED_KEYTAB=true
```

To authenticate as a principal you name, from a keytab the `aembit_agent_proxy` Linux user can read:

```shell
AEMBIT_KERBEROS_ATTESTATION_ENABLED=true
AEMBIT_AGENT_PROXY_KERBEROS_PRINCIPAL=<principal>
```

`AEMBIT_PRIVILEGED_KEYTAB=true` reads `/etc/krb5.keytab` only. Don’t combine it with `KRB5_KTNAME` or `KRB5_CLIENT_KTNAME`.

### Agent Proxy on Windows Server

To enable Kerberos attestation for [Agent Proxy on a Windows Server VM](../../deploy-install/virtual-machine/windows/agent-proxy-install-windows.md), set the following installer property:

```shell
AEMBIT_KERBEROS_ATTESTATION_ENABLED=true
```

Agent Proxy on Windows Server authenticates as the host’s computer account. `AEMBIT_PRIVILEGED_KEYTAB` has no effect on Windows Server.

### Agent Controller address on Agent Proxy

On every Agent Proxy, the host part of `AEMBIT_AGENT_CONTROLLER` must match the name in the Agent Controller’s SPN. For example, an SPN of `HTTP/agentcontroller.example.com` requires `AEMBIT_AGENT_CONTROLLER=http://agentcontroller.example.com:5000`. If the names differ, Agent Proxy can’t get a ticket for the Agent Controller.

## TLS

The contents of the communication between Agent Proxy and Agent Controller is sensitive. In a production deployment, you may configure Agent Controller TLS to secure communication between these two components using either a Customer’s PKI or Aembit’s PKI. Please see the following pages for more information on using a PKI in your configuration:

* [Configure a Customer’s PKI Agent Controller TLS](../../deploy-install/advanced-options/agent-controller/configure-customer-pki-agent-controller-tls.md)

* [Configure Aembit’s PKI Agent Controller TLS](../../deploy-install/advanced-options/agent-controller/configure-aembit-pki-agent-controller-tls.md)

## High availability

Given the critical role of attestation in evaluating an Access Policy, Aembit strongly encourages configuring multiple Agent Controllers in a high availability architecture.

To learn how, see [Agent Controller High Availability](../../deploy-install/advanced-options/agent-controller/agent-controller-high-availability.md).

The following are the additional steps you must perform for Kerberos attestation to work in a highly available configuration:

* You don’t need to join the load balancer to your domain.

* You must register the SPN `HTTP/<load balancer hostname>` on the AD account shared by all Agent Controllers in the cluster.

* You don’t need to register an SPN for each individual Agent Controller VM.

* On Windows Server, you must install every Agent Controller in the cluster with the same gMSA in `SERVICE_LOGON_ACCOUNT`.

* On Linux, you must place the keytab for the shared AD account (including the load balancer SPN) on all Agent Controller VMs.

* If you operate multiple Agent Controller clusters running behind one or more load balancers, you must register each load balancer FQDN as an SPN on the shared AD account.

* On every Agent Proxy, set `AEMBIT_AGENT_CONTROLLER` to the load balancer hostname.

## Agent Controller keytab rotation on Linux

Standard best practice recommends the periodic rotation of all keytabs. On Windows Server, a gMSA rotates its own password, so this section applies to Linux only.

Aembit shares the keytab representing an Agent Controller’s identity across multiple Agent Controller machines. The common Linux method of keytab rotation, through the System Security Services Daemon (SSSD), therefore isn’t feasible.

Your organization must orchestrate keytab rotation centrally: rotate the Agent Controller AD account keytab in one place, then push it to all Agent Controller Virtual Machines. Note that the entity performing the keytab rotation needs the appropriate permissions in AD to change the Agent Controller account password during new-keytab creation.

## Related

**Compatible credential providers**

* [API Key](../credential-providers/api-key.md)
* [AWS STS Federation](../credential-providers/aws-security-token-service-federation.md)
* [HashiCorp Vault Client Token](../credential-providers/vault-client-token.md)
* [JSON Web Token (JWT)](../credential-providers/json-web-token.md)
* [OAuth 2.0 Client Credentials](../credential-providers/oauth-client-credentials.md)
* [OIDC ID Token](../credential-providers/oidc-id-token.md)
* [Username & Password](../credential-providers/username-password.md)

**Client workload identifiers this trust supports**

* [Aembit Client ID](../client-workloads/identification/aembit-client-id.md)
* [Hostname](../client-workloads/identification/hostname.md)
* [Process Command Line](../client-workloads/identification/process-command-line.md)
* [Process Name](../client-workloads/identification/process-name.md)
* [Process Path](../client-workloads/identification/process-path.md)
* [Process User Name](../client-workloads/identification/process-user-name.md)
* [Source IP Address](../client-workloads/identification/source-ip.md)
