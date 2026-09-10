---
type: how-to
title: "Office 365 MCP Server"
description: "Configure the Office 365 MCP Server to work with AI agents through the Aembit MCP Identity Gateway."
resource: https://docs.aembit.io/ai-guide/mcp/identity-gateway/supported-servers/office365/
interface: mcp
tags: ["supported-server", "identity-gateway", "mcp"]
timestamp: 2026-08-10T15:29:42-07:00
---

# Office 365 MCP Server

Aembit supports the official [Office 365 MCP server](https://www.microsoft.com/microsoft-365), which lets AI agents access Microsoft 365 productivity data through MCP tools.

This page describes how to configure Office 365 as an MCP server behind the Aembit MCP Identity Gateway. Each user authenticates with their own Office 365 identity, and the Gateway injects their token into MCP requests.

This guide builds the Gateway-to-Server Policy—the second of the two Access Policies the MCP Identity Gateway requires. You create the first, the Client-to-Gateway Policy, during [Gateway setup](../setup-mcp-gateway.md).

## Prerequisites

Before you begin, ensure you have the following:

* A Microsoft 365 subscription with access to the M365 MCP Server
* A configured [Aembit MCP Identity Gateway](../setup-mcp-gateway.md)

## Requirements and considerations

Before you configure Office 365, review these requirements and behaviors specific to Office 365’s MCP server.

* **Administrator pre-registration required.** An administrator must pre-register the Azure App and share the Client ID and Client Secret. User-Based Auth here isn’t fully self-service like other servers.
* **Tenant-specific OAuth URLs.** The Authorization and Token URLs include your Azure **Directory (tenant) ID**, which is visible on the Azure App’s **Overview** page.
* **Register the Callback URL first.** Add the Aembit **Callback URL** to the Azure App’s **Redirect URIs** before authorization can succeed.
* **Use User-Based Auth.** Admin-Based Auth doesn’t work for Office 365.

## Register the Azure app

Office 365 doesn’t support OAuth Dynamic Client Registration, so an administrator must register an Azure App before users can authenticate. Both authentication approaches share this app, but only User-Based Auth works today.

1. Go to [Azure App registrations](https://portal.azure.com/#view/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) and click **New registration**. Enter any name, set **Supported account types** to **Accounts in this organizational directory only (Single tenant)**, set the platform to **Web**, and set the **Redirect URI** to the Aembit Credential Provider’s **Callback URL**. Copy the exact read-only value from the Credential Provider after you create it in Aembit—don’t hand-construct it.
2. On the app **Overview** page, copy the **Application (client) ID**. This is the **Client ID**.
3. Go to **Certificates & secrets > Client secrets**, click **+ New client secret**, name it, click **Add**, then copy the **Value**. This is the **Client Secret**.
4. Go to **API permissions**, click **Add a permission**, select `APIs my organization uses`, search for `07c030f6-5743-41b7-ba00-0a6e85f37c17`, select **M365 MCP Server for Claude**, check `access_as_user`, then click **Update permissions**.
5. Note the **Directory (tenant) ID** GUID on the app **Overview** page. The OAuth URLs use it as `<azure-tenant-id>`.

## Configure the Credential Provider

Create an MCP User-Based Access Token Credential Provider in Aembit.

1. Log into your Aembit Tenant.

2. Go to **Credential Providers** in the left sidebar and click **+ New**.

3. Configure the following fields:

   | Field                 | Value                                                                       |
   | --------------------- | --------------------------------------------------------------------------- |
   | **Name**              | A user-friendly name                                                        |
   | **Credential Type**   | MCP User-Based Access Token                                                 |
   | **MCP Server URL**    | `https://login.microsoftonline.com/<azure-tenant-id>/oauth2/v2.0`           |
   | **Client ID**         | The Client ID you copied earlier                                            |
   | **Client Secret**     | The Client Secret you copied earlier                                        |
   | **Scopes**            | `api://07c030f6-5743-41b7-ba00-0a6e85f37c17/.default offline_access`        |
   | **Authorization URL** | `https://login.microsoftonline.com/<azure-tenant-id>/oauth2/v2.0/authorize` |
   | **Token URL**         | `https://login.microsoftonline.com/<azure-tenant-id>/oauth2/v2.0/token`     |
   | **PKCE Required**     | On                                                                          |
   | **Resource**          | `07c030f6-5743-41b7-ba00-0a6e85f37c17`                                      |
   | **Lifetime**          | `1 year`                                                                    |

   Discover doesn't work for Office 365 — enter the **Authorization URL** and **Token URL** from the table manually, substituting your own values for any placeholders.

4. Click **Save**.

5. Copy the read-only **Callback URL** from the Credential Provider.

## Finish configuring the Azure app

OAuth Discovery doesn’t work for Office 365, so enter the **Authorization URL** and **Token URL** manually when you create the Credential Provider. Replace `<azure-tenant-id>` with the Azure **Directory (tenant) ID** from the app’s **Overview** page.

1. Set the **Authorization URL** to `https://login.microsoftonline.com/<azure-tenant-id>/oauth2/v2.0/authorize`.
2. Set the **Token URL** to `https://login.microsoftonline.com/<azure-tenant-id>/oauth2/v2.0/token`.
3. After you create the Credential Provider, copy its read-only **Callback URL** and add it to the Azure App’s **Redirect URIs**.

## Authorize the Credential Provider

There’s no administrator authorization step for this credential type. Each user completes their own Office 365 sign-in and consent the first time they access Office 365 through the Gateway. Aembit stores and refreshes each user’s tokens individually.

## Create the Server Workload

1. Go to **Server Workloads** in the left sidebar and click **+ New**.

2. Configure the following fields:

   | Field                     | Value                         |
   | ------------------------- | ----------------------------- |
   | **Name**                  | A user-friendly name          |
   | **Host**                  | `microsoft365.mcp.claude.com` |
   | **Application Protocol**  | MCP                           |
   | **Port**                  | 443 with TLS                  |
   | **URL Path**              | `/mcp`                        |
   | **Authentication method** | HTTP Authentication           |
   | **Authentication scheme** | Bearer                        |

3. Click **Save**.

## Create an Access Policy

This section creates the Gateway-to-Server Access Policy, which authorizes the MCP Identity Gateway to access Office 365 on behalf of authenticated users.

Create an Access Policy linking the MCP Identity Gateway (as the Client Workload), the Credential Provider you created, and the Server Workload for Office 365.

> **The Gateway is the Client Workload**
>
> In this policy, the Client Workload is the MCP Identity Gateway itself—not the AI agent or individual users. The AI agent connects through the separate Client-to-Gateway policy, and the Credential Provider enforces per-user access.

For step-by-step instructions, including the Client Workload settings that identify the Gateway, see [Create the gateway-to-server Access Policy](../setup-mcp-gateway.md#create-the-gateway-to-server-access-policy).

## Verify

After a user authorizes access, the Aembit **AI Access Authorized** page lists the Office 365 MCP Server as **Ready**. The AI agent can then call Office 365 MCP tools through the Gateway.
