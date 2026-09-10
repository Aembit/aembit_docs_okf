---
type: how-to
title: "Microsoft Enterprise MCP Server"
description: "Configure the Microsoft Enterprise MCP Server to work with AI agents through the Aembit MCP Identity Gateway."
resource: https://docs.aembit.io/ai-guide/mcp/identity-gateway/supported-servers/microsoft-enterprise/
interface: mcp
tags: ["supported-server", "identity-gateway", "mcp"]
timestamp: 2026-08-11T10:14:33-07:00
---

# Microsoft Enterprise MCP Server

Aembit supports the official [Microsoft Enterprise MCP server](https://learn.microsoft.com/en-us/graph/mcp-server/get-started), which lets AI agents access Microsoft enterprise data in Microsoft Graph through MCP tools.

This page describes how to configure the Microsoft Enterprise MCP Server behind the Aembit MCP Identity Gateway. Each user authenticates with their own Microsoft identity, and the Gateway injects their token into MCP requests.

This guide builds the Gateway-to-Server Policy—the second of the two Access Policies the MCP Identity Gateway requires. You create the first, the Client-to-Gateway Policy, during [Gateway setup](../setup-mcp-gateway.md).

> **Looking for direct Microsoft Graph API access?**
>
> This page covers **MCP tool access through Aembit’s MCP Identity Gateway**. To broker credentials for the Microsoft Graph REST API as a traditional Server Workload, see the [Microsoft Graph Server Workload guide](../../../../user-guide/access-policies/server-workloads/guides/microsoft-graph.md) instead.

## Prerequisites

Before you begin, ensure you have the following:

* A Microsoft Entra ID tenant with permission to register applications
* A configured [Aembit MCP Identity Gateway](../setup-mcp-gateway.md)

## Requirements and considerations

Before you configure Microsoft Enterprise MCP, review these requirements and behaviors specific to the Microsoft Enterprise MCP server.

* **Name length limits.** Copilot Studio tool calls fail with no error message when a Server Workload or Credential Provider name exceeds 15 characters, so keep both names to 15 characters or fewer. Tool names in Anthropic clients and Copilot must also be 64 characters or fewer, and MCP prefixes (server name plus tool name) can exceed this limit; short entity names reduce that risk as well.
* **Role permissions.** Granting the required API permissions needs the Application Administrator or Cloud Application Administrator role.
* **Use User-Based Auth.** Admin-Based Auth doesn’t work for this server.

## Register the Azure app

Microsoft Enterprise MCP doesn’t support OAuth Dynamic Client Registration, so an administrator must register an Azure App before users can authenticate. Both authentication approaches share this app, but only User-Based Auth works today.

1. Sign in to the Azure tenant where you want to register the MCP server.
2. Go to [Azure App registrations](https://portal.azure.com/#view/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) and click **New registration**. Enter any name, set **Supported account types** to **Accounts in this organizational directory only (Single tenant)**, set the platform to **Web**, and set the **Redirect URI** to the Aembit Credential Provider’s **Callback URL**. Copy the exact read-only value from the Credential Provider after you create it in Aembit—don’t hand-construct it.
3. On the app **Overview** page, copy the **Application (client) ID**. This is the **Client ID**.
4. Go to **Certificates & secrets > Client secrets**, click **+ New client secret**, name it, click **Add**, then copy the **Value**. This is the **Client Secret**.
5. Go to **API permissions**, click **Add a permission**, select **Microsoft MCP Server for Enterprise**, check the permissions you need, then click **Update permissions**. An administrator must grant consent for the required permissions.
6. Note the **Directory (tenant) ID** GUID on the app **Overview** page. The OAuth URLs use it as `<azure-tenant-id>`.

## Configure the Credential Provider

Create an MCP User-Based Access Token Credential Provider in Aembit.

1. Log into your Aembit Tenant.

2. Go to **Credential Providers** in the left sidebar and click **+ New**.

3. Configure the following fields:

   | Field                 | Value                                                                       |
   | --------------------- | --------------------------------------------------------------------------- |
   | **Name**              | A user-friendly name                                                        |
   | **Credential Type**   | MCP User-Based Access Token                                                 |
   | **MCP Server URL**    | `https://mcp.svc.cloud.microsoft/enterprise`                                |
   | **Client ID**         | The Client ID you copied earlier                                            |
   | **Client Secret**     | The Client Secret you copied earlier                                        |
   | **Scopes**            | `api://e8c77dc2-69b3-43f4-bc51-3213c9d915b4/.default offline_access`        |
   | **Authorization URL** | `https://login.microsoftonline.com/<azure-tenant-id>/oauth2/v2.0/authorize` |
   | **Token URL**         | `https://login.microsoftonline.com/<azure-tenant-id>/oauth2/v2.0/token`     |
   | **PKCE Required**     | On                                                                          |
   | **Resource**          | `e8c77dc2-69b3-43f4-bc51-3213c9d915b4`                                      |
   | **Lifetime**          | `1 year`                                                                    |

   Discover doesn't work for Microsoft Enterprise MCP — enter the **Authorization URL** and **Token URL** from the table manually, substituting your own values for any placeholders.

4. Click **Save**.

5. Copy the read-only **Callback URL** from the Credential Provider.

## Finish configuring the Azure app

OAuth Discovery doesn’t work for Microsoft Enterprise MCP, so enter the **Authorization URL** and **Token URL** manually when you create the Credential Provider. Replace `<azure-tenant-id>` with the Azure **Directory (tenant) ID** from the app’s **Overview** page.

1. Set the **Authorization URL** to `https://login.microsoftonline.com/<azure-tenant-id>/oauth2/v2.0/authorize`.
2. Set the **Token URL** to `https://login.microsoftonline.com/<azure-tenant-id>/oauth2/v2.0/token`.
3. After you create the Credential Provider, copy its read-only **Callback URL** and add it to the Azure App’s **Redirect URIs**.

## Authorize the Credential Provider

There’s no administrator authorization step for this credential type. Each user completes their own Microsoft sign-in and consent the first time they access Microsoft Enterprise MCP through the Gateway. Aembit stores and refreshes each user’s tokens individually.

## Create the Server Workload

1. Go to **Server Workloads** in the left sidebar and click **+ New**.

2. Configure the following fields:

   | Field                     | Value                     |
   | ------------------------- | ------------------------- |
   | **Name**                  | A user-friendly name      |
   | **Host**                  | `mcp.svc.cloud.microsoft` |
   | **Application Protocol**  | MCP                       |
   | **Port**                  | 443 with TLS              |
   | **URL Path**              | `/enterprise`             |
   | **Authentication method** | HTTP Authentication       |
   | **Authentication scheme** | Bearer                    |

3. Click **Save**.

## Create an Access Policy

This section creates the Gateway-to-Server Access Policy, which authorizes the MCP Identity Gateway to access Microsoft Enterprise MCP on behalf of authenticated users.

Create an Access Policy linking the MCP Identity Gateway (as the Client Workload), the Credential Provider you created, and the Server Workload for Microsoft Enterprise MCP.

> **The Gateway is the Client Workload**
>
> In this policy, the Client Workload is the MCP Identity Gateway itself—not the AI agent or individual users. The AI agent connects through the separate Client-to-Gateway policy, and the Credential Provider enforces per-user access.

For step-by-step instructions, including the Client Workload settings that identify the Gateway, see [Create the gateway-to-server Access Policy](../setup-mcp-gateway.md#create-the-gateway-to-server-access-policy).

## Verify

After a user authorizes access, the Aembit **AI Access Authorized** page lists the Microsoft Enterprise MCP Server as **Ready**. The AI agent can then call Microsoft Enterprise MCP tools through the Gateway.
