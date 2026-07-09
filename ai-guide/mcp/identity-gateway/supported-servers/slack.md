---
type: how-to
title: "Slack MCP Server"
description: "Configure the Slack MCP Server to work with AI agents through the Aembit MCP Identity Gateway."
resource: https://docs.aembit.io/ai-guide/mcp/identity-gateway/supported-servers/slack/
interface: mcp
tags: [supported-server, identity-gateway, mcp]
timestamp: 2026-06-30T12:35:49-04:00
---

# Slack MCP Server

Aembit supports the official [Slack MCP server](https://docs.slack.dev/ai/slack-mcp-server/), which lets AI agents**AI Agent**: A software workload that authenticates to systems, requests credentials, and accesses resources, either on behalf of a person or on its own. Aembit secures AI agents with the same identity-first model it uses for any workload. User-driven agents such as Claude Desktop also carry a blended identity that ties access to both the user and the agent.[Learn more](../../../../get-started/use-cases/ai-agents.md) search and read workspace content through MCP**Model Context Protocol**: A standard protocol for AI agent and server interactions that defines how AI assistants communicate with external tools and data sources.[Learn more(opens in new tab)](https://modelcontextprotocol.io/) tools.

This page describes how to configure Slack as an MCP server**MCP Server**: A server that implements the Model Context Protocol to provide tools, resources, or data to AI agents and MCP clients.[Learn more(opens in new tab)](https://modelcontextprotocol.io/specification/2025-03-26/server) behind the Aembit MCP Identity Gateway**MCP Identity Gateway**: A component that brokers MCP traffic between MCP clients and target MCP servers, validating authorization and presenting Aembit-managed credentials on each request.[Learn more](../overview.md). Each user authenticates with their own Slack identity, and the Gateway injects their token into MCP requests.

Looking for direct Slack API access?

This page covers **MCP tool access through Aembit’s MCP Identity Gateway**. To broker credentials for Slack’s REST API as a traditional Server Workload**Server Workload**: Server Workloads represent target services, APIs, databases, or applications that receive and respond to access requests from Client Workloads.[Learn more](../../../../get-started/concepts/server-workloads.md), see the [Slack Server Workload guide](../../../../user-guide/access-policies/server-workloads/guides/slack.md) instead.

## Prerequisites

[Section titled “Prerequisites”](#prerequisites)

Before you begin, ensure you have the following:

* A Slack workspace where you can create and install a Slack app
* A configured [Aembit MCP Identity Gateway](../setup-mcp-gateway.md)

## Requirements and considerations

[Section titled “Requirements and considerations”](#requirements-and-considerations)

Before you configure Slack, review these requirements and behaviors specific to Slack’s MCP server.

* **Use User-Based Auth.** Slack returns an `invalid_scope` error during the Admin-Based (OAuth 2.0 Authorization Code) flow, so this guide uses the MCP User-Based Access Token credential type.
* **Register an internal or Marketplace app.** Slack lets only internal apps and apps published in the Slack Marketplace use MCP; it rejects unlisted apps.
* **Connect over Streamable HTTP.** Slack’s MCP server supports only the Streamable HTTP transport, not Server-Sent Events (SSE).

## Create the app

[Section titled “Create the app”](#create-the-app)

Slack doesn’t support OAuth Dynamic Client Registration**Dynamic Client Registration**: An OAuth mechanism that allows MCP clients to register with the Authorization Server at runtime without pre-configuration, receiving unique credentials for subsequent authorization requests.[Learn more](../../authorization-server/concepts-mcp-auth-server.md#client-registration), so an administrator must register an app before users can authenticate.

1. Go to [Slack API: Your Apps](https://api.slack.com/apps), click **Create New App**, then choose **From Scratch**.

2. Enter an app name, select your workspace, then click **Create**.

3. On the **App Credentials** screen, copy the **Client ID** and **Client Secret**, and store them for the next section.

4. Go to **Agents & AI Apps** and enable the **Model Context Protocol** toggle.

5. Go to **OAuth & Permissions** and opt in to **PKCE**. Keep this page open. You add the Aembit Callback URL and scopes after you create the Credential Provider.

## Configure the Credential Provider

[Section titled “Configure the Credential Provider”](#configure-the-credential-provider)

Create an MCP User-Based Access Token Credential Provider**Credential Provider**: Credential Providers obtain the specific access credentials—such as API keys, OAuth tokens, or temporary cloud credentials—that Client Workloads need to authenticate to Server Workloads.[Learn more](../../../../get-started/concepts/credential-providers.md) in Aembit.

1. Log into your Aembit Tenant.

2. Go to **Credential Providers** in the left sidebar and click **+ New**.

3. Configure the following fields:

   | Field               | Value                                                          |
   | ------------------- | -------------------------------------------------------------- |
   | **Name**            | A user-friendly name                                           |
   | **Credential Type** | MCP User-Based Access Token                                    |
   | **MCP Server URL**  | `https://mcp.slack.com/mcp`                                    |
   | **Client ID**       | The Client ID you copied from Slack                            |
   | **Client Secret**   | The Client Secret you copied from Slack                        |
   | **Scopes**          | `search:read.public channels:history channels:read users:read` |
   | **PKCE Required**   | On                                                             |

   For **MCP Server URL**, click **Discover** to populate the Authorization URL and Token URL.

4. Click **Save**.

5. Copy the read-only **Callback URL** from the Credential Provider.

## Finish configuring the app

[Section titled “Finish configuring the app”](#finish-configuring-the-app)

Return to the Slack app’s **OAuth & Permissions** page.

1. Under **Redirect URLs**, click **Add New Redirect URL**, paste the Aembit **Callback URL**, click **Add**, then click **Save URLs**.

2. Under **Scopes**, add the same scopes you set on the Credential Provider.

3. Install and approve the app in your Slack workspace.

## Authorize the Credential Provider

[Section titled “Authorize the Credential Provider”](#authorize-the-credential-provider)

1. Return to the Credential Provider in Aembit and click **Authorize**.

2. Complete the Slack sign-in and approve access. The Credential Provider status changes to **Ready** when the flow completes.

## Create the Server Workload

[Section titled “Create the Server Workload”](#create-the-server-workload)

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

[Section titled “Create an Access Policy”](#create-an-access-policy)

Create an Access Policy**Access Policy**: Access Policies define, enforce, and audit access between Client and Server Workloads by cryptographically verifying workload identity and contextual factors rather than relying on static secrets.[Learn more](../../../../get-started/concepts/access-policies.md) linking your Client Workload**Client Workload**: Client Workloads represent software applications, scripts, or automated processes that initiate access requests to Server Workloads, operating autonomously without direct user interaction.[Learn more](../../../../get-started/concepts/client-workloads.md) (the AI agent), the MCP User-Based Access Token Credential Provider, and the Slack Server Workload. See [Access Policies](../../../../user-guide/access-policies/overview.md) for details.

## Verify

[Section titled “Verify”](#verify)

After a user authorizes access, the Aembit **AI Access Authorized** page lists the Slack MCP Server as **Ready**. The AI agent can then call Slack MCP tools through the Gateway.
