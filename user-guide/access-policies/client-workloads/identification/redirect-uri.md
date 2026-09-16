---
type: how-to
title: "Redirect URI"
description: "How to identify MCP client workloads using a Redirect URI within Aembit"
resource: https://docs.aembit.io/user-guide/access-policies/client-workloads/identification/redirect-uri/
interface: web-ui
tags: ["mcp", "identification", "client-workload", "access-policy"]
timestamp: 2026-09-15T18:18:13-07:00
---

# Redirect URI

This page explains how to use the **Redirect URI** identifier to identify MCP client workloads in Aembit.

## Understanding the Redirect URI identifier

In OAuth 2.1, a redirect URI is the callback URL where the Authorization Server sends users after they authenticate. When an MCP client registers through Dynamic Client Registration (DCR), it provides its redirect URI.

In Aembit, the redirect URI serves a dual purpose: it’s both the OAuth callback URL and the identifier for your Client Workload. This enables granular Access Policies based on which MCP client is requesting access.

For example, if Gemini CLI registers with `http://localhost:7777/oauth/callback`, you configure a Client Workload with the Redirect URI identifier set to that value. This ensures only authorized MCP clients can obtain access tokens for your protected MCP servers.

For more about how redirect URIs work in MCP authorization flows, see [Redirect URIs](../../../deploy-install/mcp-authorization-server/concepts-mcp-auth-server.md#redirect-uris).

## Applicable deployment type

This method is for [MCP Authorization Server](../../../deploy-install/mcp-authorization-server/overview.md) deployments. It identifies MCP clients that connect to MCP servers protected by Aembit’s MCP Authorization Server.

## Create a Client Workload with a Redirect URI identifier

To configure a Client Workload using the Redirect URI identifier, follow these steps:

1. Log into your Aembit Tenant.

2. In the sidebar, click **Client Workloads**.

3. Click **+ New** to open the Client Workload form.

4. Enter the **Name** and optional **Description** for the Client Workload.

5. Under **Client Identification**, select **Redirect URI**.

   For **Value**, enter the redirect URI that your MCP client uses for OAuth callbacks.

   For example: `http://localhost:7777/oauth/callback`

   Optionally, add additional redirect URIs by clicking **+** to allow multiple MCP clients or multiple redirect URIs for a single client. If you don’t know the redirect URI for your MCP client, see [Find the redirect URI](#find-the-redirect-uri).

   > **Redirect URI scope**
   >
   > Redirect URI is one of the identifier types available for MCP Client Workloads; the other is [CIMD Client ID](cimd-client-id.md). A Client Workload uses a single MCP identifier type, and you can’t combine it with other identifier types such as Hostname or Source IP Address to narrow the scope. You can add multiple redirect URIs to a single Client Workload, but each URI grants access through the associated Access Policy using OR logic. Any MCP client whose redirect URI matches one of the configured values receives access.
   >
   > To maintain precise control, assign one redirect URI per Client Workload and create separate Access Policies for each MCP client you want to manage independently.

6. Under **MCP Authorization Configuration**, configure the **Enforce SSO** option:

   * **On** (default) - Users must authenticate through a configured identity provider during the OAuth flow. When Enforce SSO is on, a multi-select dropdown appears listing the SSO identity providers configured for your tenant. By default, Aembit selects all identity providers. Deselect any that you don’t want to appear on the MCP authentication page for this Client Workload.
   * **Off** - The OAuth flow completes without user authentication.

   For architecture diagrams comparing these flows, see [MCP Authorization Server architecture](../../../deploy-install/mcp-authorization-server/concepts-mcp-auth-server.md#mcp-authorization-server-architecture).

7. Click **Save**.

   Aembit displays the new Client Workload on the **Client Workloads** page.

> **Port numbers for loopback URIs**
>
> For loopback IP addresses (`localhost` and `127.0.0.1`), Aembit ignores the port number during redirect URI matching. A Client Workload configured with `http://localhost:7777/oauth/callback` matches requests from any port, like `http://localhost:8080/oauth/callback`. This aligns with the MCP specification’s support for ephemeral ports.

## Find the redirect URI

Each MCP client uses a specific redirect URI for OAuth callbacks. The following table lists redirect URIs for common MCP clients. Check your MCP client’s documentation to confirm its redirect URI.

**Local development:**

| MCP client    | Redirect URI                           |
| ------------- | -------------------------------------- |
| MCP Inspector | `http://localhost:6274/oauth/callback` |
| Gemini CLI    | `http://localhost:7777/oauth/callback` |

**Remote or cloud-hosted:**

| MCP client     | Redirect URI                                  |
| -------------- | --------------------------------------------- |
| Claude Desktop | `https://claude.ai/api/mcp/auth_callback`     |
| Custom web app | `https://your-app.example.com/oauth/callback` |

For custom MCP clients, check the client’s OAuth configuration or documentation for its redirect URI.

## Related

**Compatible trust providers**

* [OIDC ID Token](../../trust-providers/oidc-id-token-trust-provider.md)
* [SAMLv2 Response](../../trust-providers/saml-response-trust-provider.md)
