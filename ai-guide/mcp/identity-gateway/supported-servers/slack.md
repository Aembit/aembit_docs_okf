---
type: how-to
title: "Slack MCP Server"
description: "Configure the Slack MCP Server to work with AI agents through the Aembit MCP Identity Gateway."
resource: https://docs.aembit.io/ai-guide/mcp/identity-gateway/supported-servers/slack/
interface: mcp
tags: ["supported-server", "identity-gateway", "mcp"]
timestamp: 2026-08-10T14:01:53-07:00
---

# Slack MCP Server

Aembit supports the official [Slack MCP server](https://docs.slack.dev/ai/slack-mcp-server/), which lets AI agents search and read workspace content through MCP tools.

This page describes how to configure Slack as an MCP server behind the Aembit MCP Identity Gateway. Each user authenticates with their own Slack identity, and the Gateway injects their token into MCP requests.

This guide builds the Gateway-to-Server Policy—the second of the two Access Policies the MCP Identity Gateway requires. You create the first, the Client-to-Gateway Policy, during [Gateway setup](../setup-mcp-gateway.md).

> **Looking for direct Slack API access?**
>
> This page covers **MCP tool access through Aembit’s MCP Identity Gateway**. To broker credentials for Slack’s REST API as a traditional Server Workload, see the [Slack Server Workload guide](../../../../user-guide/access-policies/server-workloads/guides/slack.md) instead.

## Prerequisites

Before you begin, ensure you have the following:

* A Slack workspace where you can create and install a Slack app
* A configured [Aembit MCP Identity Gateway](../setup-mcp-gateway.md)

## Requirements and considerations

Before you configure Slack, review these requirements and behaviors specific to Slack’s MCP server.

* **Restricted app types.** Slack lets only internal apps and apps published in the Slack Marketplace use MCP; it rejects unlisted apps.
* **No SSE transport.** Slack’s MCP server supports only the Streamable HTTP transport, not Server-Sent Events (SSE).
* **Use User-Based Auth.** Aembit supports User-Based Auth for Slack.

## Create the app

Slack doesn’t support OAuth Dynamic Client Registration, so an administrator must register a Slack app before users can authenticate.

1. Go to [Slack API: Your Apps](https://api.slack.com/apps), click **Create New App**, then choose **From Scratch**.
2. Enter an app name, select your workspace, then click **Create**.
3. On the **App Credentials** screen, copy the **Client ID** and **Client Secret**, and store them for the Credential Provider configuration.
4. Go to **Agents & AI Apps** and enable the **Model Context Protocol** toggle.
5. Go to **OAuth & Permissions** and opt in to **PKCE**. Keep this page open—you add the Aembit Callback URL and scopes after you create the Credential Provider.

## Configure the Credential Provider

Create an MCP User-Based Access Token Credential Provider in Aembit.

1. Log into your Aembit Tenant.

2. Go to **Credential Providers** in the left sidebar and click **+ New**.

3. Configure the following fields:

   | Field               | Value                                                          |
   | ------------------- | -------------------------------------------------------------- |
   | **Name**            | A user-friendly name                                           |
   | **Credential Type** | MCP User-Based Access Token                                    |
   | **MCP Server URL**  | `https://mcp.slack.com/mcp`                                    |
   | **Client ID**       | The Client ID you copied earlier                               |
   | **Client Secret**   | The Client Secret you copied earlier                           |
   | **Scopes**          | `search:read.public channels:history channels:read users:read` |
   | **PKCE Required**   | On                                                             |

   For **MCP Server URL**, click **Discover** to populate the Authorization URL and Token URL.

4. Click **Save**.

5. Copy the read-only **Callback URL** from the Credential Provider.

## Finish configuring the app

After you create the Credential Provider, copy its read-only **Callback URL** and return to the Slack app’s **OAuth & Permissions** page.

1. Under **Redirect URLs**, click **Add New Redirect URL**, paste the Aembit **Callback URL**, click **Add**, then click **Save URLs**.
2. Under **Scopes**, add the same scopes you set on the Credential Provider: `search:read.public channels:history channels:read users:read`
3. Install and approve the app in your Slack workspace when prompted.

## Authorize the Credential Provider

1. Return to the Credential Provider in Aembit and click **Authorize**.

2. Complete the Slack sign-in and approve access. The Credential Provider status changes to **Ready** when the flow completes.

## Create the Server Workload

1. Go to **Server Workloads** in the left sidebar and click **+ New**.

2. Configure the following fields:

   | Field                     | Value                |
   | ------------------------- | -------------------- |
   | **Name**                  | A user-friendly name |
   | **Host**                  | `mcp.slack.com`      |
   | **Application Protocol**  | MCP                  |
   | **Port**                  | 443 with TLS         |
   | **URL Path**              | `/mcp`               |
   | **Authentication method** | HTTP Authentication  |
   | **Authentication scheme** | Bearer               |

3. Click **Save**.

## Create an Access Policy

This section creates the Gateway-to-Server Access Policy, which authorizes the MCP Identity Gateway to access Slack on behalf of authenticated users.

Create an Access Policy linking the MCP Identity Gateway (as the Client Workload), the Credential Provider you created, and the Server Workload for Slack.

> **The Gateway is the Client Workload**
>
> In this policy, the Client Workload is the MCP Identity Gateway itself—not the AI agent or individual users. The AI agent connects through the separate Client-to-Gateway policy, and the Credential Provider enforces per-user access.

For step-by-step instructions, including the Client Workload settings that identify the Gateway, see [Create the gateway-to-server Access Policy](../setup-mcp-gateway.md#create-the-gateway-to-server-access-policy).

## Verify

After a user authorizes access, the Aembit **AI Access Authorized** page lists the Slack MCP Server as **Ready**. The AI agent can then call Slack MCP tools through the Gateway.
