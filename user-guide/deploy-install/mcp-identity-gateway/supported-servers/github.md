---
type: how-to
title: "GitHub MCP Server"
description: "Configure the GitHub MCP Server to work with AI agents through the Aembit MCP Identity Gateway."
resource: https://docs.aembit.io/user-guide/deploy-install/mcp-identity-gateway/supported-servers/github/
interface: mcp
tags: ["supported-server", "mcp-identity-gateway", "deploy-install"]
timestamp: 2026-09-15T18:18:13-07:00
---

# GitHub MCP Server

Aembit supports the official [GitHub MCP server](https://github.com/github/github-mcp-server), which lets AI agents work with repositories, issues, and pull requests through MCP tools.

This page describes how to configure GitHub as an MCP server behind the Aembit MCP Identity Gateway.

This guide builds the Gateway-to-Server Policy—the second of the two Access Policies the MCP Identity Gateway requires. You create the first, the Client-to-Gateway Policy, during [Gateway setup](../../../access-policies/mcp-identity-gateway/setup-mcp-gateway.md).

> **Looking for direct GitHub API access?**
>
> This page covers **MCP tool access through Aembit’s MCP Identity Gateway**. To broker credentials for GitHub’s REST API as a traditional Server Workload, see the [GitHub Server Workload guide](../../../access-policies/server-workloads/guides/github-rest.md) instead.

## Prerequisites

Before you begin, ensure you have the following:

* A GitHub account with a GitHub Copilot subscription (Individual, Business, or Enterprise)
* A configured [Aembit MCP Identity Gateway](../../../access-policies/mcp-identity-gateway/setup-mcp-gateway.md)

## Requirements and considerations

Before you configure GitHub, review these requirements and behaviors specific to GitHub’s MCP server.

* **Admin-Based Auth requires a GitHub App, not an OAuth App.** GitHub OAuth Apps only issue an access token—no refresh token. Admin-Based Auth requires both, so use a GitHub App and keep the **Expire user authorization tokens** checkbox checked when creating it.
* **Copilot subscription required.** The GitHub MCP server is part of GitHub Copilot; users need a Copilot subscription (Individual, Business, or Enterprise).

## Choose an authentication approach

Aembit supports two authentication approaches for GitHub. The tabs in the following sections stay in sync with the approach you choose.

* **User-Based**: each user authenticates with their own GitHub identity, and the Gateway injects that user’s token into MCP requests. Aembit stores and refreshes each user’s tokens individually.
* **Admin-Based**: an administrator completes the OAuth flow once during setup, and all users who access GitHub through the Gateway share the resulting tokens.

## Choose a GitHub app type

GitHub doesn’t support OAuth Dynamic Client Registration, so an administrator must register an app before users can authenticate. The two authentication approaches require **different app types**. User-Based Auth uses a GitHub **OAuth App**, while Admin-Based Auth requires a GitHub **App**—OAuth Apps don’t issue the refresh tokens that Admin-Based Auth needs.

Using the GitHub MCP server also requires a GitHub Copilot subscription (Individual, Business, or Enterprise).

## Configure the Credential Provider

* User-Based

  Create an MCP User-Based Access Token Credential Provider in Aembit.

  1. Log into your Aembit Tenant.

  2. Go to **Credential Providers** in the left sidebar and click **+ New**.

  3. Configure the following fields:

     | Field               | Value                                |
     | ------------------- | ------------------------------------ |
     | **Name**            | A user-friendly name                 |
     | **Credential Type** | MCP User-Based Access Token          |
     | **MCP Server URL**  | `https://api.githubcopilot.com/mcp`  |
     | **Client ID**       | The Client ID you copied earlier     |
     | **Client Secret**   | The Client Secret you copied earlier |
     | **PKCE Required**   | On                                   |
     | **Lifetime**        | `1 year`                             |

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
     | **OAuth URL**       | `https://github.com/login/oauth`                       |
     | **PKCE Required**   | On                                                     |
     | **Lifetime**        | `1 year`                                               |

     For **OAuth URL**, click **URL Discovery** to populate the Authorization URL and Token URL.

  4. Click **Save**.

  5. Copy the read-only **Callback URL** from the Credential Provider.

## Create the GitHub app

* User-Based

  User-based auth uses a GitHub **OAuth App**.

  1. Go to **GitHub Settings > Developer settings > OAuth Apps** and click **New OAuth App**.
  2. Set the **Authorization callback URL** to the Aembit Credential Provider’s **Callback URL**. Copy the exact read-only value from the Credential Provider after you create it in Aembit.
  3. Copy the **Client ID** and generate a **Client Secret**, and store them for the Credential Provider configuration.
  4. To support additional Aembit Tenants, add each Tenant’s Callback URL to the OAuth App settings.

* Admin-Based

  Admin-Based Auth requires a GitHub **App**, not an OAuth App—OAuth Apps issue only an access token, and Admin-Based Auth needs a refresh token too.

  1. Go to **GitHub Settings > Developer settings > GitHub Apps** and click **New GitHub App**.
  2. Set the **Callback URL** to the Aembit Credential Provider’s **Callback URL**. Copy the exact read-only value from the Credential Provider after you create it in Aembit.
  3. Keep the **Expire user authorization tokens** checkbox checked—this setting causes GitHub to issue refresh tokens.
  4. Copy the **Client ID** and generate a **Client Secret**, and store them for the Credential Provider configuration.

## Authorize the Credential Provider

* User-Based

  There’s no administrator authorization step for this credential type. Each user completes their own GitHub sign-in and consent the first time they access GitHub through the Gateway, and Aembit stores and refreshes each user’s tokens individually.

* Admin-Based

  1. Return to the Credential Provider in Aembit and click **Authorize**.

  2. Complete the GitHub sign-in as an administrator and approve access. The Credential Provider status changes to **Ready** when the flow completes.

## Create the Server Workload

1. Go to **Server Workloads** in the left sidebar and click **+ New**.

2. Configure the following fields:

   | Field                     | Value                   |
   | ------------------------- | ----------------------- |
   | **Name**                  | A user-friendly name    |
   | **Host**                  | `api.githubcopilot.com` |
   | **Application Protocol**  | MCP                     |
   | **Port**                  | 443 with TLS            |
   | **URL Path**              | `/mcp`                  |
   | **Authentication method** | HTTP Authentication     |
   | **Authentication scheme** | Bearer                  |

3. Click **Save**.

## Create an Access Policy

This section creates the Gateway-to-Server Access Policy, which authorizes the MCP Identity Gateway to access GitHub on behalf of authenticated users.

Create an Access Policy linking the MCP Identity Gateway (as the Client Workload), the Credential Provider you created, and the Server Workload for GitHub.

> **The Gateway is the Client Workload**
>
> In this policy, the Client Workload is the MCP Identity Gateway itself—not the AI agent or individual users. The AI agent connects through the separate Client-to-Gateway policy, and the Credential Provider enforces per-user access.

For step-by-step instructions, including the Client Workload settings that identify the Gateway, see [Create the gateway-to-server Access Policy](../../../access-policies/mcp-identity-gateway/setup-mcp-gateway.md#create-the-gateway-to-server-access-policy).

## Verify

After authorization completes, the Aembit **AI Access Authorized** page lists the GitHub MCP Server as **Ready**. The AI agent can then call GitHub MCP tools through the Gateway.
