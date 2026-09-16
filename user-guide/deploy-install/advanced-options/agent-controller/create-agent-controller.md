---
type: how-to
title: "How to create an Agent Controller"
resource: https://docs.aembit.io/user-guide/deploy-install/advanced-options/agent-controller/create-agent-controller/
interface: web-ui
tags: ["agent-controller", "advanced-option", "deploy-install"]
timestamp: 2026-09-15T20:39:46-07:00
---

# How to create an Agent Controller

The Agent Controller is a helper component that facilitates the registration of other Aembit Edge Components. This page details how to create a new Agent Controller in your Aembit Tenant.

## Create an Agent Controller

To create an Agent Controller in your Aembit Tenant, follow these steps:

1. Log into your Aembit Tenant, and go to **Edge Components -> Agent Controllers**. ![New in Agent Controllers section](https://docs.aembit.io/_astro/agent_controller_create_entry_point_ac.IXW0t43H_ZgxUYl.webp)

2. Click **+ New**, which displays the **Agent Controller** pop out menu.

3. Fill out the following fields:

   * **Name** - Choose a user-friendly name for your controller.

   * **Description (optional)** - Add a brief description to help identify its purpose.

   * **Trust Provider** - Select an existing Trust Provider from the dropdown menu.

     If you don’t have a Trust Provider set up, refer to [Add Trust Provider](../../../access-policies/trust-providers/add-trust-provider.md) to create one.

     > **Note**
     >
     > Trust Providers enable identity attestation during workload registration. Associating your Agent Controller to a Trust Provider accomplishes this for you.
     >
     > This makes sure there is secure, verified communication between components. Aembit recommends configuring a Trust Provider as part of your setup.
     >
     > The dropdown lists AWS Role, AWS Metadata Service, Azure Metadata Service, and Kubernetes Service Account Trust Providers only. The [Kerberos Trust Provider](../../../access-policies/trust-providers/kerberos-trust-provider.md) attests Agent Proxies and can’t register an Agent Controller; register on-premises Agent Controllers with a Device Code.

   * **Allowed TLS Hostname (Optional)** - Enter the FQDN (Ex: `my-subdomain.my-domain.com`), subdomain, or wildcard domain (Ex: `*.example.com`) to include in the [Aembit Managed TLS](configure-aembit-pki-agent-controller-tls.md) certificate. This restricts the certificate to only be valid when Agent Proxies attempt to access Agent Controller using this specific domain name.

     The allowed TLS hostname is unique to each Agent Controller that you configure it on.

4. Click **Save**.

   Once you save it, your newly created Agent Controller appears in the list of available Agent Controllers.
