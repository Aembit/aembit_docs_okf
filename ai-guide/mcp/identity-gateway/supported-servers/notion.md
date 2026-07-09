---
type: how-to
title: "Notion MCP Server"
description: "Configure the Notion MCP Server to work with AI agents through the Aembit MCP Identity Gateway."
resource: https://docs.aembit.io/ai-guide/mcp/identity-gateway/supported-servers/notion/
interface: mcp
tags: [supported-server, identity-gateway, mcp]
timestamp: 2026-06-30T12:35:49-04:00
---

# Notion MCP Server

Aembit supports the official [Notion MCP server](https://developers.notion.com/guides/mcp/overview), which lets AI agents**AI Agent**: A software workload that authenticates to systems, requests credentials, and accesses resources, either on behalf of a person or on its own. Aembit secures AI agents with the same identity-first model it uses for any workload. User-driven agents such as Claude Desktop also carry a blended identity that ties access to both the user and the agent.[Learn more](../../../../get-started/use-cases/ai-agents.md) search and read workspace content through MCP**Model Context Protocol**: A standard protocol for AI agent and server interactions that defines how AI assistants communicate with external tools and data sources.[Learn more(opens in new tab)](https://modelcontextprotocol.io/) tools.

This page describes how to configure Notion as an MCP server**MCP Server**: A server that implements the Model Context Protocol to provide tools, resources, or data to AI agents and MCP clients.[Learn more(opens in new tab)](https://modelcontextprotocol.io/specification/2025-03-26/server) behind the Aembit MCP Identity Gateway**MCP Identity Gateway**: A component that brokers MCP traffic between MCP clients and target MCP servers, validating authorization and presenting Aembit-managed credentials on each request.[Learn more](../overview.md). Each user authenticates with their own Notion identity, and the Gateway injects their token into MCP requests.

## Prerequisites

[Section titled “Prerequisites”](#prerequisites)

Before you begin, ensure you have the following:

* A Notion workspace where you can create an integration
* A configured [Aembit MCP Identity Gateway](../setup-mcp-gateway.md)

## Requirements and considerations

[Section titled “Requirements and considerations”](#requirements-and-considerations)

Before you configure Notion, review these requirements and behaviors specific to Notion’s MCP server.

* **Create a Public integration.** Notion’s OAuth flow requires a Public integration. Internal integrations and Personal Access Tokens (`ntn_*`) work only with the deprecated local (stdio) Notion MCP server, not the remote server Aembit connects to.
* **Aembit refreshes tokens automatically.** Notion issues short-lived (one-hour) access tokens with rotating refresh tokens. Aembit refreshes them in the background, so users don’t need to re-authorize each hour.

## Create the integration

[Section titled “Create the integration”](#create-the-integration)

Notion requires a registered Public integration before users can authenticate through OAuth.

1. Go to [Notion integrations](https://www.notion.so/profile/integrations) and click **New integration**.

2. Select the **Public** integration type, enter a name and the required organization and contact details, then create the integration.

3. Copy the **OAuth client ID** and **OAuth client secret**, and store them for the next section. Keep the integration settings open. You add the Aembit Redirect URI after you create the Credential Provider.

## Configure the Credential Provider

[Section titled “Configure the Credential Provider”](#configure-the-credential-provider)

Create an MCP User-Based Access Token Credential Provider**Credential Provider**: Credential Providers obtain the specific access credentials—such as API keys, OAuth tokens, or temporary cloud credentials—that Client Workloads need to authenticate to Server Workloads.[Learn more](../../../../get-started/concepts/credential-providers.md) in Aembit.

1. Log into your Aembit Tenant.

2. Go to **Credential Providers** in the left sidebar and click **+ New**.

3. Configure the following fields:

   | Field               | Value                                                |
   | ------------------- | ---------------------------------------------------- |
   | **Name**            | A user-friendly name                                 |
   | **Credential Type** | MCP User-Based Access Token                          |
   | **MCP Server URL**  | `https://mcp.notion.com/mcp`                         |
   | **Client ID**       | The OAuth client ID from your Notion integration     |
   | **Client Secret**   | The OAuth client secret from your Notion integration |
   | **PKCE Required**   | On                                                   |

   For **MCP Server URL**, click **Discover** to populate the Authorization URL and Token URL.

4. Click **Save**.

5. Copy the read-only **Callback URL** from the Credential Provider.

## Finish configuring the integration

[Section titled “Finish configuring the integration”](#finish-configuring-the-integration)

Return to the Notion integration’s settings.

1. Under the OAuth configuration, add the Aembit **Callback URL** as a **Redirect URI**, then save.

## Authorize the Credential Provider

[Section titled “Authorize the Credential Provider”](#authorize-the-credential-provider)

1. Return to the Credential Provider in Aembit and click **Authorize**.

2. Complete the Notion sign-in, select the workspace and pages to share, then approve access. The Credential Provider status changes to **Ready** when the flow completes.

## Create the Server Workload

[Section titled “Create the Server Workload”](#create-the-server-workload)

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

[Section titled “Create an Access Policy”](#create-an-access-policy)

Create an Access Policy**Access Policy**: Access Policies define, enforce, and audit access between Client and Server Workloads by cryptographically verifying workload identity and contextual factors rather than relying on static secrets.[Learn more](../../../../get-started/concepts/access-policies.md) linking your Client Workload**Client Workload**: Client Workloads represent software applications, scripts, or automated processes that initiate access requests to Server Workloads, operating autonomously without direct user interaction.[Learn more](../../../../get-started/concepts/client-workloads.md) (the AI agent), the MCP User-Based Access Token Credential Provider, and the Notion Server Workload. See [Access Policies](../../../../user-guide/access-policies/overview.md) for details.

## Verify

[Section titled “Verify”](#verify)

After a user authorizes access, the Aembit **AI Access Authorized** page lists the Notion MCP Server as **Ready**. The AI agent can then call Notion MCP tools through the Gateway.
