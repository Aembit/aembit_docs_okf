---
type: how-to
title: "Configure Agent Controller TLS with Aembit's PKI"
description: "How to configure Agent Controller TLS with Aembit's PKI in Kubernetes environments and Virtual Machine deployments"
resource: https://docs.aembit.io/user-guide/deploy-install/advanced-options/agent-controller/configure-aembit-pki-agent-controller-tls/
interface: web-ui
tags: [agent-controller, advanced-option, deploy-install]
timestamp: 2026-05-19T10:29:14-07:00
type_inferred: true
---

# Configure Agent Controller TLS with Aembit's PKI

Using Aembit’s PKI for Agent Controller TLS certificates enables you to have secure Agent-Proxy-to-Agent-Controller communication in Kubernetes environments and on Virtual Machine deployments.

## Configure Agent Controller TLS with Aembit’s PKI in Kubernetes

[Section titled “Configure Agent Controller TLS with Aembit’s PKI in Kubernetes”](#configure-agent-controller-tls-with-aembits-pki-in-kubernetes)

If you have a Kubernetes deployment and would like to use Aembit’s PKI, there are two configuration options.

### Automatic TLS configuration

[Section titled “Automatic TLS configuration”](#automatic-tls-configuration)

If you *aren’t already* using a custom PKI, install the latest Aembit Helm Chart. By default, Agent Controllers are automatically configured to accept TLS communication from Agent Proxy.

### Preserve existing custom configuration

[Section titled “Preserve existing custom configuration”](#preserve-existing-custom-configuration)

If you have already configured custom PKI-based Agent Controller TLS, no additional steps are necessary, as Aembit preserves your configuration.

## Configure Aembit’s PKI-based Agent Controller for VM deployments

[Section titled “Configure Aembit’s PKI-based Agent Controller for VM deployments”](#configure-aembits-pki-based-agent-controller-for-vm-deployments)

If you are using a Virtual Machine, Agent Controller won’t know which hostname Agent Proxy should use to communicate with Agent Controller. This requires you to manually configure Agent Controller to enable TLS communication between Agent Proxy and Agent Controller.

### Aembit Tenant configuration

[Section titled “Aembit Tenant configuration”](#aembit-tenant-configuration)

1. Log into your Aembit Tenant, and go to **Edge Components -> Agent Controllers**.

2. Select or create a new Agent Controller.

3. In **Allowed TLS Hostname (Optional)**, enter the FQDN (Ex: `my-subdomain.my-domain.com`), subdomain, or wildcard domain (Ex: `*.example.com`) to use for the Aembit Managed TLS certificate.

   Note

   The allowed TLS hostname is unique to each Agent Controller that you configure it on.

4. Click **Save**.

### Manual configuration

[Section titled “Manual configuration”](#manual-configuration)

If you haven’t already configured Aembit’s PKI, perform the these steps:

1. Install Agent Controller on your Virtual Machine, and set the `AEMBIT_MANAGED_TLS_HOSTNAME` environment variable to the hostname that Agent Proxy uses to communicate with Agent Controller. When set, Agent Controller retrieves the certificate for the hostname from Aembit Cloud, enabling TLS communication between Agent Proxy and Agent Controller.

   Note

   When you enable TLS on Agent Controller version 1.24.xxxx or later, Agent Controller automatically:

   * opens port 443 (5443 on VM) for HTTPS traffic

   * doesn’t open port 80 (5000 on VM) for HTTP traffic

   As of Agent Controller version 1.29.xxxx, on Kubernetes deployments, Agent Controller exposes Prometheus metrics over HTTP on port 9090 regardless of TLS state. On Virtual Machine deployments, enabling TLS continues to switch Prometheus metrics from HTTP port 9090 to HTTPS port 9091.

2. Configure Agent Proxy’s Virtual Machines to trust the Aembit Tenant Root Certificate Authority (CA).

## Confirming TLS status

[Section titled “Confirming TLS status”](#confirming-tls-status)

When you have configured Agent Controller TLS, you can verify the status of Agent Controller TLS by performing the following steps:

1. Log into your Aembit Tenant.

2. Click on the **Edge Components** link in the left sidebar. Aembit displays the **Edge Components** dashboard.

   ![Edge Components Agent Controller Status Page](https://docs.aembit.io/_astro/agent_controller_tls_status_page.BAU687gU_1vYFl9.webp)

3. Aembit displays the **Agent Controllers** tab. You should see a list of your configured Agent Controllers.

4. Verify TLS is active by confirming color status button in the TLS column for the Agent Controller.

   Note

   If the TLS status isn’t colored, this means TLS isn’t configured for Agent Controller.

## Agent Controller TLS support matrix

[Section titled “Agent Controller TLS support matrix”](#agent-controller-tls-support-matrix)

The following table lists the different Agent Controller TLS deployment models, denoting whether the configuration process is manual or automatic.

| Agent Controller Deployment Model | Customer Based PKI | Aembit Based PKI |
| --------------------------------- | ------------------ | ---------------- |
| Kubernetes                        | Manual             | Automatic        |
| Virtual Machine                   | Manual             | Manual           |
| ECS                               | Not Supported      | Automatic        |

## Automatic TLS certificate rotation

[Section titled “Automatic TLS certificate rotation”](#automatic-tls-certificate-rotation)

Aembit-managed certificates are automatically rotated by the Agent Controller, with no manual steps or extra configuration required.
