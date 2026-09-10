---
type: how-to
title: "Kensho MCP Server"
description: "Configure the Kensho MCP Server to work with AI agents through the Aembit MCP Identity Gateway."
resource: https://docs.aembit.io/ai-guide/mcp/identity-gateway/supported-servers/kensho/
interface: mcp
tags: ["supported-server", "identity-gateway", "mcp"]
timestamp: 2026-08-11T09:54:00-07:00
---

# Kensho MCP Server

Aembit supports the official [Kensho MCP server](https://kensho.com), which lets AI agents query Kensho financial market data through MCP tools.

This page describes how to configure Kensho as an MCP server behind the Aembit MCP Identity Gateway.

This guide builds the Gateway-to-Server Policy—the second of the two Access Policies the MCP Identity Gateway requires. You create the first, the Client-to-Gateway Policy, during [Gateway setup](../setup-mcp-gateway.md).

## Prerequisites

Before you begin, ensure you have the following:

* An active Kensho subscription (Kensho provisions accounts by invitation only; contact [Kensho](https://kensho.com) to request access)
* A configured [Aembit MCP Identity Gateway](../setup-mcp-gateway.md)

## Requirements and considerations

Before you configure Kensho, review these requirements and behaviors specific to the Kensho MCP server.

* **Short token lifetime.** Kensho access and refresh tokens expire after 1 week. If the refresh token expires due to inactivity, the user must re-authorize via Aembit.

## Choose an authentication approach

Aembit supports two authentication approaches for Kensho. The tabs in the following sections stay in sync with the approach you choose.

* **User-Based**: each user authenticates with their own Kensho identity, and the Gateway injects that user’s token into MCP requests. Aembit stores and refreshes each user’s tokens individually.
* **Admin-Based**: an administrator completes the OAuth flow once during setup, and all users who access Kensho through the Gateway share the resulting tokens.

## Configure the Credential Provider

* User-Based

  Create an MCP User-Based Access Token Credential Provider in Aembit.

  1. Log into your Aembit Tenant.

  2. Go to **Credential Providers** in the left sidebar and click **+ New**.

  3. Configure the following fields:

     | Field               | Value                                          |
     | ------------------- | ---------------------------------------------- |
     | **Name**            | A user-friendly name                           |
     | **Credential Type** | MCP User-Based Access Token                    |
     | **MCP Server URL**  | `https://kfinance.kensho.com/integrations/mcp` |
     | **Client ID**       | Auto-populated—no pre-registration needed      |
     | **Client Secret**   | Auto-populated by Discover                     |
     | **Scopes**          | `kensho:app:kfinance offline_access`           |
     | **PKCE Required**   | On                                             |

     For **MCP Server URL**, click **Discover** to populate the Authorization URL and Token URL.

  4. Click **Save**.

  5. Copy the read-only **Callback URL** from the Credential Provider.

* Admin-Based

  Create an OAuth 2.0 Authorization Code Credential Provider in Aembit.

  1. Log into your Aembit Tenant.

  2. Go to **Credential Providers** in the left sidebar and click **+ New**.

  3. Configure the following fields:

     | Field               | Value                                                  |
     | ------------------- | ------------------------------------------------------ |
     | **Name**            | A user-friendly name                                   |
     | **Credential Type** | OAuth 2.0 Authorization Code                           |
     | **Client ID**       | The Client ID you copied earlier                       |
     | **Client Secret**   | The Client Secret, if required—see the app setup notes |
     | **Scopes**          | `kensho:app:kfinance offline_access`                   |
     | **OAuth URL**       | `https://kfinance.kensho.com/integrations`             |
     | **PKCE Required**   | On                                                     |
     | **Lifetime**        | `1 week`                                               |

     For **OAuth URL**, click **URL Discovery** to populate the Authorization URL and Token URL.

  4. Click **Save**.

  5. Copy the read-only **Callback URL** from the Credential Provider.

## Finish configuring the OAuth client

* User-Based

  Kensho’s MCP server supports OAuth Dynamic Client Registration, so you don’t need to register an app with Kensho. When you click **Discover** on the Credential Provider, Aembit registers the OAuth client automatically. Kensho grants access when the user completes the OAuth consent flow in the browser.

* Admin-Based

  Admin-Based Auth needs a Client ID. The Kensho MCP server supports Dynamic Client Registration, so there’s no app to create in a console. Instead, an administrator obtains a Client ID with a one-time registration request to the Kensho registration endpoint.

  1. Create the Credential Provider in Aembit and copy its read-only **Callback URL**.

  2. Run the following request, replacing `<credential-provider-callback-url>` with the Callback URL you copied and `<tenantId>` with your Aembit tenant:

     ```plaintext
     curl -X POST "https://kfinance.kensho.com/integrations/register" \
       -H "Content-Type: application/json" \
       -d '{
         "redirect_uris": ["<credential-provider-callback-url>"],
         "grant_types": ["authorization_code", "refresh_token"],
         "response_types": ["code"],
         "token_endpoint_auth_method": "none",
         "application_type": "native",
         "client_name": "<tenantId>.aembit.io"
       }'
     ```

  3. Copy the returned `client_id` into the Credential Provider configuration.

  4. When the Credential Provider asks for a **Client Secret**, enter any value—the registration is a public client.

## Authorize the Credential Provider

* User-Based

  There’s no administrator authorization step for this credential type. Each user completes their own Kensho sign-in and consent the first time they access Kensho through the Gateway, and Aembit stores and refreshes each user’s tokens individually.

* Admin-Based

  1. Return to the Credential Provider in Aembit and click **Authorize**.

  2. Complete the Kensho sign-in as an administrator and approve access. The Credential Provider status changes to **Ready** when the flow completes.

## Create the Server Workload

1. Go to **Server Workloads** in the left sidebar and click **+ New**.

2. Configure the following fields:

   | Field                     | Value                 |
   | ------------------------- | --------------------- |
   | **Name**                  | A user-friendly name  |
   | **Host**                  | `kfinance.kensho.com` |
   | **Application Protocol**  | MCP                   |
   | **Port**                  | 443 with TLS          |
   | **URL Path**              | `/integrations/mcp`   |
   | **Authentication method** | HTTP Authentication   |
   | **Authentication scheme** | Bearer                |

3. Click **Save**.

## Create an Access Policy

This section creates the Gateway-to-Server Access Policy, which authorizes the MCP Identity Gateway to access Kensho on behalf of authenticated users.

Create an Access Policy linking the MCP Identity Gateway (as the Client Workload), the Credential Provider you created, and the Server Workload for Kensho.

> **The Gateway is the Client Workload**
>
> In this policy, the Client Workload is the MCP Identity Gateway itself—not the AI agent or individual users. The AI agent connects through the separate Client-to-Gateway policy, and the Credential Provider enforces per-user access.

For step-by-step instructions, including the Client Workload settings that identify the Gateway, see [Create the gateway-to-server Access Policy](../setup-mcp-gateway.md#create-the-gateway-to-server-access-policy).

## Verify

After authorization completes, the Aembit **AI Access Authorized** page lists the Kensho MCP Server as **Ready**. The AI agent can then call Kensho MCP tools through the Gateway.
