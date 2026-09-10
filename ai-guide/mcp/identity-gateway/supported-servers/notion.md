---
type: how-to
title: "Notion MCP Server"
description: "Configure the Notion MCP Server to work with AI agents through the Aembit MCP Identity Gateway."
resource: https://docs.aembit.io/ai-guide/mcp/identity-gateway/supported-servers/notion/
interface: mcp
tags: ["supported-server", "identity-gateway", "mcp"]
timestamp: 2026-08-10T14:01:53-07:00
---

# Notion MCP Server

Aembit supports the official [Notion MCP server](https://developers.notion.com/guides/mcp/overview), which lets AI agents search and read workspace content through MCP tools.

This page describes how to configure Notion as an MCP server behind the Aembit MCP Identity Gateway. Each user authenticates with their own Notion identity, and the Gateway injects their token into MCP requests.

This guide builds the Gateway-to-Server Policy—the second of the two Access Policies the MCP Identity Gateway requires. You create the first, the Client-to-Gateway Policy, during [Gateway setup](../setup-mcp-gateway.md).

## Prerequisites

Before you begin, ensure you have the following:

* A Notion workspace where you can create an integration
* A configured [Aembit MCP Identity Gateway](../setup-mcp-gateway.md)

## Requirements and considerations

Before you configure Notion, review these requirements and behaviors specific to Notion’s MCP server.

* **Internal integration tokens don’t work remotely.** Personal or internal integration tokens (`ntn_****`) work only with Notion’s local (stdio) MCP server, not the remote one. Notion is prioritizing the remote server and may deprecate the local stdio server, so always use the remote MCP server URL.
* **Use User-Based Auth.** Aembit supports User-Based Auth for Notion.

## Create the integration

Notion doesn’t support OAuth Dynamic Client Registration, so an administrator must create a Public Integration before users can authenticate.

1. Go to [Notion integrations](https://www.notion.so/profile/integrations) and click **New integration**.
2. Set the type to **Public**. This enables OAuth; internal or private integrations don’t support OAuth.
3. Under **OAuth Domain & URIs**, add the Aembit Credential Provider’s **Callback URL**. Copy the exact read-only value from the Credential Provider after you create it in Aembit.
4. Save the integration, then note the **Client ID** and **Client Secret** for the Credential Provider configuration.

## Configure the Credential Provider

Create an MCP User-Based Access Token Credential Provider in Aembit.

1. Log into your Aembit Tenant.

2. Go to **Credential Providers** in the left sidebar and click **+ New**.

3. Configure the following fields:

   | Field               | Value                                |
   | ------------------- | ------------------------------------ |
   | **Name**            | A user-friendly name                 |
   | **Credential Type** | MCP User-Based Access Token          |
   | **MCP Server URL**  | `https://mcp.notion.com/mcp`         |
   | **Client ID**       | The Client ID you copied earlier     |
   | **Client Secret**   | The Client Secret you copied earlier |
   | **PKCE Required**   | On                                   |

   For **MCP Server URL**, click **Discover** to populate the Authorization URL and Token URL.

4. Click **Save**.

5. Copy the read-only **Callback URL** from the Credential Provider.

## Finish configuring the integration

After you create the Credential Provider, copy its read-only **Callback URL** and return to the Notion Public Integration.

1. Under **OAuth Domain & URIs**, add the Aembit **Callback URL**, then save the integration.

## Authorize the Credential Provider

1. Return to the Credential Provider in Aembit and click **Authorize**.

2. Complete the Notion sign-in, select the workspace and pages to share, then approve access. The Credential Provider status changes to **Ready** when the flow completes.

## Create the Server Workload

1. Go to **Server Workloads** in the left sidebar and click **+ New**.

2. Configure the following fields:

   | Field                     | Value                |
   | ------------------------- | -------------------- |
   | **Name**                  | A user-friendly name |
   | **Host**                  | `mcp.notion.com`     |
   | **Application Protocol**  | MCP                  |
   | **Port**                  | 443 with TLS         |
   | **URL Path**              | `/mcp`               |
   | **Authentication method** | HTTP Authentication  |
   | **Authentication scheme** | Bearer               |

3. Click **Save**.

## Create an Access Policy

This section creates the Gateway-to-Server Access Policy, which authorizes the MCP Identity Gateway to access Notion on behalf of authenticated users.

Create an Access Policy linking the MCP Identity Gateway (as the Client Workload), the Credential Provider you created, and the Server Workload for Notion.

> **The Gateway is the Client Workload**
>
> In this policy, the Client Workload is the MCP Identity Gateway itself—not the AI agent or individual users. The AI agent connects through the separate Client-to-Gateway policy, and the Credential Provider enforces per-user access.

For step-by-step instructions, including the Client Workload settings that identify the Gateway, see [Create the gateway-to-server Access Policy](../setup-mcp-gateway.md#create-the-gateway-to-server-access-policy).

## Verify

After a user authorizes access, the Aembit **AI Access Authorized** page lists the Notion MCP Server as **Ready**. The AI agent can then call Notion MCP tools through the Gateway.
