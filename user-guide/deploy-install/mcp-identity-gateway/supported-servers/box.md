---
type: how-to
title: "Box MCP Server"
description: "Configure the Box MCP Server to work with AI agents through the Aembit MCP Identity Gateway."
resource: https://docs.aembit.io/user-guide/deploy-install/mcp-identity-gateway/supported-servers/box/
interface: mcp
tags: ["supported-server", "mcp-identity-gateway", "deploy-install"]
timestamp: 2026-09-15T18:18:13-07:00
---

# Box MCP Server

Aembit supports the official [Box MCP server](https://developer.box.com/guides/box-mcp/), which lets AI agents read and manage Box files and folders through MCP tools.

This page describes how to configure Box as an MCP server behind the Aembit MCP Identity Gateway.

This guide builds the Gateway-to-Server Policy—the second of the two Access Policies the MCP Identity Gateway requires. You create the first, the Client-to-Gateway Policy, during [Gateway setup](../../../access-policies/mcp-identity-gateway/setup-mcp-gateway.md).

> **Looking for direct Box API access?**
>
> This page covers **MCP tool access through Aembit’s MCP Identity Gateway**. To broker credentials for the Box API as a traditional Server Workload, see the [Box Server Workload guide](../../../access-policies/server-workloads/guides/box.md) instead.

## Prerequisites

Before you begin, ensure you have the following:

* A Box account with access to the Box Developer Console (a free developer account is sufficient)
* A configured [Aembit MCP Identity Gateway](../../../access-policies/mcp-identity-gateway/setup-mcp-gateway.md)

Box doesn’t support OAuth Dynamic Client Registration, so you also create a Box custom app in the Box Developer Console. This guide walks through that in [Create the Box custom app](#create-the-box-custom-app), before you configure the Credential Provider.

## Requirements and considerations

Before you configure Box, review these requirements and behaviors specific to Box’s MCP server.

* **Use User-Based Auth.** Aembit supports User-Based Auth for Box.
* **No Dynamic Client Registration.** Box needs the OAuth app you create in the Box Developer Console.
* **User-Based Auth requires PKCE turned off.** Unlike most servers, Box’s User-Based flow fails with PKCE enabled. Set **PKCE Required** to **Off** on the MCP User-Based Access Token Credential Provider.

## Create the Box custom app

Box doesn’t support OAuth Dynamic Client Registration, so an administrator must create one custom app before users can authenticate. You do this setup once per Box account.

1. Go to the [Box Developer Console](https://app.box.com/developers/console) and click **New Custom App**.
2. Select **User Authentication (OAuth 2.0)** as the authentication method, then name the app and create it.
3. Copy the **Client ID** and generate a **Client Secret** from the app’s **Configuration** tab.
4. Leave the Developer Console open. After you create the Credential Provider in Aembit, you’ll return here to add its callback URL under **OAuth 2.0 Redirect URI**.

A free Box developer account is enough to create this app—a paid enterprise account isn’t required.

## Configure the Credential Provider

Create an MCP User-Based Access Token Credential Provider in Aembit.

1. Log into your Aembit Tenant.

2. Go to **Credential Providers** in the left sidebar and click **+ New**.

3. Configure the following fields:

   | Field               | Value                                |
   | ------------------- | ------------------------------------ |
   | **Name**            | A user-friendly name                 |
   | **Credential Type** | MCP User-Based Access Token          |
   | **MCP Server URL**  | `https://mcp.box.com`                |
   | **Client ID**       | The Client ID you copied earlier     |
   | **Client Secret**   | The Client Secret you copied earlier |
   | **PKCE Required**   | Off                                  |
   | **Lifetime**        | `1 year`                             |

   For **MCP Server URL**, click **Discover** to populate the Authorization URL and Token URL.

   Leave **PKCE Required** set to **Off**. Box is the only supported server whose User-Based flow fails with PKCE enabled.

4. Click **Save**.

5. Copy the read-only **Callback URL** from the Credential Provider.

## Finish configuring the Box app

This step uses the custom app you create in the prerequisites.

1. Return to your app in the [Box Developer Console](https://app.box.com/developers/console) and open the **Configuration** tab.
2. Under **OAuth 2.0 Redirect URI**, add the read-only **Callback URL** you copied from the MCP User-Based Access Token Credential Provider. Its path ends in `/userauth/<credential-provider-id>/callback`.
3. Save the app configuration.

## Authorize the Credential Provider

There’s no administrator authorization step for this credential type. Each user completes their own Box sign-in and provides consent the first time they access Box through the Gateway, and Aembit stores and refreshes each user’s tokens individually.

## Create the Server Workload

1. Go to **Server Workloads** in the left sidebar and click **+ New**.

2. Configure the following fields:

   | Field                     | Value                |
   | ------------------------- | -------------------- |
   | **Name**                  | A user-friendly name |
   | **Host**                  | `mcp.box.com`        |
   | **Application Protocol**  | MCP                  |
   | **Port**                  | 443 with TLS         |
   | **URL Path**              | ``                   |
   | **Authentication method** | HTTP Authentication  |
   | **Authentication scheme** | Bearer               |

3. Click **Save**.

## Create an Access Policy

This section creates the Gateway-to-Server Access Policy, which authorizes the MCP Identity Gateway to access Box on behalf of authenticated users.

Create an Access Policy linking the MCP Identity Gateway (as the Client Workload), the Credential Provider you created, and the Server Workload for Box.

> **The Gateway is the Client Workload**
>
> In this policy, the Client Workload is the MCP Identity Gateway itself—not the AI agent or individual users. The AI agent connects through the separate Client-to-Gateway policy, and the Credential Provider enforces per-user access.

For step-by-step instructions, including the Client Workload settings that identify the Gateway, see [Create the gateway-to-server Access Policy](../../../access-policies/mcp-identity-gateway/setup-mcp-gateway.md#create-the-gateway-to-server-access-policy).

## Verify

After authorization completes, the Aembit **AI Access Authorized** page lists the Box MCP Server as **Ready**. The AI agent can then call Box MCP tools through the Gateway.
