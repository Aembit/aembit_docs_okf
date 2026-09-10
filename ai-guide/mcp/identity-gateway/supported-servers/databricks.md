---
type: how-to
title: "Databricks MCP Server"
description: "Configure the Databricks MCP Server to work with AI agents through the Aembit MCP Identity Gateway."
resource: https://docs.aembit.io/ai-guide/mcp/identity-gateway/supported-servers/databricks/
interface: mcp
tags: ["supported-server", "identity-gateway", "mcp"]
timestamp: 2026-08-10T16:34:42-07:00
---

# Databricks MCP Server

Aembit supports the official [Databricks MCP servers](https://docs.databricks.com/aws/en/generative-ai/mcp), which let AI agents query Databricks data through MCP tools.

This page describes how to configure Databricks as an MCP server behind the Aembit MCP Identity Gateway.

This guide builds the Gateway-to-Server Policy—the second of the two Access Policies the MCP Identity Gateway requires. You create the first, the Client-to-Gateway Policy, during [Gateway setup](../setup-mcp-gateway.md).

> **Looking for direct Databricks API access?**
>
> This page covers **MCP tool access through Aembit’s MCP Identity Gateway**. To broker credentials for the Databricks REST API as a traditional Server Workload, see the [Databricks Server Workload guide](../../../../user-guide/access-policies/server-workloads/guides/databricks.md) instead.

## Prerequisites

Before you begin, ensure you have the following:

* A Databricks workspace on a paid plan
* A configured [Aembit MCP Identity Gateway](../setup-mcp-gateway.md)

## Requirements and considerations

Before you configure Databricks, review these requirements and behaviors specific to the Databricks MCP server.

* **Workspace-specific URL.** The MCP host and OAuth Discover URL include the workspace hostname, which differs per workspace. Each workspace needs its own Server Workload in Aembit.
* **Short default token lifetime.** The default access token TTL is 60 minutes and the refresh token TTL is about 7 days. Both are configurable in the App Connection settings. If the refresh token expires, the user must reauthorize.

## Choose an authentication approach

Aembit supports two authentication approaches for Databricks. The tabs in the following sections stay in sync with the approach you choose.

* **User-Based**: each user authenticates with their own Databricks identity, and the Gateway injects that user’s token into MCP requests. Aembit stores and refreshes each user’s tokens individually.
* **Admin-Based**: an administrator completes the OAuth flow once during setup, and all users who access Databricks through the Gateway share the resulting tokens.

## Create the OAuth app connection

Databricks doesn’t support OAuth Dynamic Client Registration, so an administrator must pre-register one OAuth App Connection in the Databricks Account Console before users can authenticate. Both authentication approaches share this connection.

1. Log in to the [Databricks Account Console](https://accounts.cloud.databricks.com/).

2. Click the **Settings** icon in the sidebar.

3. Open the **App connections** tab.

4. Click **Add connection** and configure it:

   * Give the connection any name.
   * Set **Redirect URLs** to the Aembit Credential Provider’s **Callback URL**. Copy the exact read-only value from the Credential Provider after you create it in Aembit.
   * Set **Access scopes** to the APIs you need (for example, `ALL APIs`).
   * Check **Generate a client secret**.
   * Token TTLs are optional; the defaults are 60 minutes for access tokens and 10080 minutes (about 7 days) for refresh tokens.

5. After saving, copy the **Client ID** and **Client Secret** from the connection details.

## Configure the Credential Provider

* User-Based

  Create an MCP User-Based Access Token Credential Provider in Aembit.

  1. Log into your Aembit Tenant.

  2. Go to **Credential Providers** in the left sidebar and click **+ New**.

  3. Configure the following fields:

     | Field               | Value                                                   |
     | ------------------- | ------------------------------------------------------- |
     | **Name**            | A user-friendly name                                    |
     | **Credential Type** | MCP User-Based Access Token                             |
     | **MCP Server URL**  | `https://<account_id>.cloud.databricks.com/api/2.0/mcp` |
     | **Client ID**       | The Client ID you copied earlier                        |
     | **Client Secret**   | The Client Secret you copied earlier                    |
     | **Scopes**          | `all-apis offline_access`                               |
     | **PKCE Required**   | On                                                      |
     | **Lifetime**        | `1 year`                                                |

     For **MCP Server URL**, click **Discover** to populate the Authorization URL and Token URL.

  4. Click **Save**.

  5. Copy the read-only **Callback URL** from the Credential Provider.

* Admin-Based

  Create an OAuth 2.0 Authorization Code Credential Provider in Aembit.

  1. Log into your Aembit Tenant.

  2. Go to **Credential Providers** in the left sidebar and click **+ New**.

  3. Configure the following fields:

     | Field               | Value                                                   |
     | ------------------- | ------------------------------------------------------- |
     | **Name**            | A user-friendly name                                    |
     | **Credential Type** | OAuth 2.0 Authorization Code                            |
     | **Client ID**       | The Client ID you copied earlier                        |
     | **Client Secret**   | The Client Secret, if required—see the app setup notes  |
     | **Scopes**          | `all-apis offline_access`                               |
     | **OAuth URL**       | `https://<account_id>.cloud.databricks.com/api/2.0/mcp` |
     | **PKCE Required**   | On                                                      |
     | **Lifetime**        | `1 year`                                                |

     For **OAuth URL**, click **URL Discovery** to populate the Authorization URL and Token URL.

  4. Click **Save**.

  5. Copy the read-only **Callback URL** from the Credential Provider.

## Finish configuring the OAuth app connection

* User-Based

  User-Based Auth uses the shared OAuth App Connection you create in the prerequisites—both authentication approaches use the same connection.

  1. Create the MCP User-Based Access Token Credential Provider in Aembit and copy its read-only **Callback URL**.
  2. Confirm the App Connection’s **Redirect URLs** include that Callback URL (see the prerequisites).
  3. Supply the connection’s **Client ID** and **Client Secret** in the Credential Provider.

* Admin-Based

  Admin-Based Auth uses the same shared OAuth App Connection you create in the prerequisites—both authentication approaches use the same connection.

  1. Create the MCP Credential Provider (OAuth 2.0 Authorization Code) in Aembit and copy its read-only **Callback URL**.
  2. Confirm the App Connection’s **Redirect URLs** include that Callback URL (see the prerequisites).
  3. Supply the connection’s **Client ID** and **Client Secret** in the Credential Provider.

## Authorize the Credential Provider

* User-Based

  There’s no administrator authorization step for this credential type. Each user completes their own Databricks sign-in and consent the first time they access Databricks through the Gateway, and Aembit stores and refreshes each user’s tokens individually.

* Admin-Based

  1. Return to the Credential Provider in Aembit and click **Authorize**.

  2. Complete the Databricks sign-in as an administrator and approve access. The Credential Provider status changes to **Ready** when the flow completes.

## Create the Server Workload

1. Go to **Server Workloads** in the left sidebar and click **+ New**.

2. Configure the following fields:

   | Field                     | Value                               |
   | ------------------------- | ----------------------------------- |
   | **Name**                  | A user-friendly name                |
   | **Host**                  | `<account_id>.cloud.databricks.com` |
   | **Application Protocol**  | MCP                                 |
   | **Port**                  | 443 with TLS                        |
   | **URL Path**              | `/api/2.0/mcp/sql`                  |
   | **Authentication method** | HTTP Authentication                 |
   | **Authentication scheme** | Bearer                              |

3. Click **Save**.

## Create an Access Policy

This section creates the Gateway-to-Server Access Policy, which authorizes the MCP Identity Gateway to access Databricks on behalf of authenticated users.

Create an Access Policy linking the MCP Identity Gateway (as the Client Workload), the Credential Provider you created, and the Server Workload for Databricks.

> **The Gateway is the Client Workload**
>
> In this policy, the Client Workload is the MCP Identity Gateway itself—not the AI agent or individual users. The AI agent connects through the separate Client-to-Gateway policy, and the Credential Provider enforces per-user access.

For step-by-step instructions, including the Client Workload settings that identify the Gateway, see [Create the gateway-to-server Access Policy](../setup-mcp-gateway.md#create-the-gateway-to-server-access-policy).

## Verify

After authorization completes, the Aembit **AI Access Authorized** page lists the Databricks MCP Server as **Ready**. The AI agent can then call Databricks MCP tools through the Gateway.
