---
type: how-to
title: "How to enable mTLS on a Server Workload"
description: "Enable outbound mTLS from the Aembit Agent Proxy to a Server Workload using X.509-SVID certificates."
resource: https://docs.aembit.io/user-guide/access-policies/server-workloads/enable-mtls/
interface: web-ui
tags: ["server-workload", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# How to enable mTLS on a Server Workload

Agent Proxy can establish outbound mTLS connections to your Server Workloads using SPIFFE-compliant X.509-SVID certificates. Agent Proxy generates the private key in memory, obtains a signed leaf certificate and chain from Aembit Cloud, and presents the certificate during the Transport Layer Security (TLS) handshake.

This page describes the end-to-end workflow and walks through the Server Workload side of the configuration. The matching Credential Provider configuration lives at [Create an X.509-SVID Credential Provider](../credential-providers/spiffe-x509-svid.md).

> **Note**
>
> mTLS using X.509-SVID requires Agent Proxy v1.31 or later. Older Agent Proxy versions ignore the directive that requests a Certificate Signing Request (CSR).

## Prerequisites

Before you enable mTLS on a Server Workload, you must have the following:

* Agent Proxy **v1.31 or later**.
* An **X.509-SVID Credential Provider** is configured in your Aembit Tenant. See [Create an X.509-SVID Credential Provider](../credential-providers/spiffe-x509-svid.md).
* A **Standalone Certificate Authority** is available to sign X.509-SVID certificates. See [Configure a Standalone CA](../../deploy-install/advanced-options/tls-decrypt/configure-tls-decrypt-standalone-ca.md) to create and assign one.
* The destination Server Workload supports validating SPIFFE-compliant X.509-SVID certificates.
* The Server Workload uses the HTTP application protocol.

> **Paid feature**
>
> Standalone CAs are a paid feature. Contact your Aembit representative for pricing and to enable Standalone CAs on your Aembit Tenant.

## Enable mTLS

To enable mTLS on a Server Workload, configure the Server Workload’s authentication method and then create the Access Policy that connects your Client Workload to it.

### Configure the mTLS Authentication method

The Server Workload must use the **mTLS Authentication** method so that it validates the client certificate the Agent Proxy presents.

1. Log into your Aembit Tenant.

2. Click **Server Workloads**.

3. Create a new Server Workload, or select an existing Server Workload from the list and click **Edit**.

4. Under **Authentication**, select **mTLS Authentication**. Aembit sets the **Scheme** to **x509 Certificate**.

   ![Server Workload edit panel with Authentication Method set to mTLS Authentication and Scheme set to x509 Certificate](https://docs.aembit.io/_astro/server-workload-mtls-edit.W_g4YgfK_Ow00Q.webp)

5. Click **Save**.

For details on Server Workload authentication methods, see [Authentication methods and schemes](authentication.md).

### Create the Access Policy

Connect your Client Workload to the Server Workload using an X.509-SVID Credential Provider.

1. In the Aembit Tenant, click **Access Policies** in the left sidebar, then click **+ New**.

2. For **Client Workload**, select the workload that originates the mTLS connection.

3. For **Server Workload**, select the workload you configured for mTLS Authentication.

4. For **Credential Provider**, select your X.509-SVID Credential Provider.

5. Click **Save**, then activate the policy.

When the Client Workload next initiates a connection through Agent Proxy, the proxy fetches a signed X.509-SVID certificate, completes the mTLS handshake with the Server Workload, and proxies the connection.

## Related pages

* [Create an X.509-SVID Credential Provider](../credential-providers/spiffe-x509-svid.md)
* [About the X.509-SVID Credential Provider](../credential-providers/about-spiffe-x509-svid.md)
* [Authentication methods and schemes](authentication.md)
* [Credential lifecycle](credential-lifecycle.md)
* [Enable TLS on a Server Workload](server-workload-enable-tls.md)
