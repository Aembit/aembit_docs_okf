---
type: how-to
title: "Datadog MCP Server"
description: "Configure the Datadog MCP Server to work with AI agents through the Aembit MCP Identity Gateway."
resource: https://docs.aembit.io/user-guide/deploy-install/mcp-identity-gateway/supported-servers/datadog/
interface: mcp
tags: ["supported-server", "mcp-identity-gateway", "deploy-install"]
timestamp: 2026-11-09T14:11:42-08:00
---

# Datadog MCP Server

Aembit supports the official [Datadog MCP server](https://docs.datadoghq.com/mcp_server/), which lets AI agents query monitors, logs, metrics, and other Datadog data through MCP tools.

The following steps configure Datadog as an MCP server behind the Aembit MCP Identity Gateway. Each user authenticates with their own Datadog identity, and MCP Identity Gateway injects their token into MCP requests.

This guide builds the Gateway-to-Server Policy—the second of the two Access Policies the MCP Identity Gateway requires. You create the first, the Client-to-Gateway Policy, during [Gateway setup](../../../access-policies/mcp-identity-gateway/setup-mcp-gateway.md).

## Prerequisites

Before you begin, ensure you have the following:

* A Datadog administrator who can change **Organization Settings**, to enable MCP access and add the Callback URL to the redirect URL allowlist
* A configured [Aembit MCP Identity Gateway](../../../access-policies/mcp-identity-gateway/setup-mcp-gateway.md)

## Requirements and considerations

Before you configure Datadog, review these requirements and behaviors specific to the Datadog MCP server.

* **Use User-Based Auth.** Aembit supports User-Based Auth for Datadog.
* **Allowlist the Callback URL first.** Until the Credential Provider’s **Callback URL** is on your organization’s **MCP OAuth Redirect URLs** list, user authorization fails with `invalid_redirect_uri`, even though **Discover** succeeds.
* **Match your Datadog site.** The MCP server host must belong to the site that hosts your Datadog organization. A host for another site fails to connect or authorize.
* **No government sites.** Datadog does not offer the MCP server on the `US1-FED` and `US2-FED` sites.
* **Network restrictions apply.** If your organization enables the Datadog IP allowlist, allow the network address that MCP Identity Gateway connects from.

## Enable the MCP server in Datadog

A Datadog organization administrator turns on MCP access and grants the MCP permissions before users can authenticate. Each Datadog site serves the MCP server from its own host, so find yours first.

1. Find your organization’s [Datadog site](https://docs.datadoghq.com/getting_started/site/) and its MCP server host. Use this host wherever this guide shows `mcp.<datadog-site>`.

   | Datadog site | MCP server host         |
   | ------------ | ----------------------- |
   | US1          | `mcp.datadoghq.com`     |
   | US3          | `mcp.us3.datadoghq.com` |
   | US5          | `mcp.us5.datadoghq.com` |
   | EU1          | `mcp.datadoghq.eu`      |
   | AP1          | `mcp.ap1.datadoghq.com` |
   | AP2          | `mcp.ap2.datadoghq.com` |
   | UK1          | `mcp.uk1.datadoghq.com` |

2. In Datadog, go to **Organization Settings** and enable **MCP Access**. Enable **MCP Write Access** too if users need tools that create or change resources.

3. Confirm that each user’s role has the `mcp_read` permission, and `mcp_write` for write tools. The Datadog Standard Role includes both. For a custom role, go to **Organization Settings > Roles**, edit the role, and select **MCP Read** and **MCP Write**.

4. Grant the standard Datadog permissions for the data each user’s tools reach, such as **Monitors Read** for monitor queries.

For details, see [MCP Server setup](https://docs.datadoghq.com/mcp_server/setup/) in the Datadog documentation.

## Configure the Credential Provider

Datadog registers the OAuth client when you run **Discover**, so you do not create an application in Datadog. Datadog accepts only the redirect URLs your organization allowlists, so you add the Callback URL of the MCP User-Based Access Token Credential Provider to that allowlist.

1. Log in to your Aembit Tenant.
2. Go to **Credential Providers** and click **+ New**.
3. Enter a **Name** and select **MCP User-Based Access Token** as the **Credential Type**.
4. Set **MCP Server URL** to `https://mcp.<datadog-site>/v1/mcp`, using the host for your Datadog site. Click **Discover** to populate the **Client Id**, **Authorization URL**, and **Token URL**.
5. Make sure **PKCE Required** is on, then click **Save**.
6. Copy the read-only **Callback URL** from the saved Credential Provider.
7. In Datadog, go to **Organization Settings** and open **MCP OAuth Redirect URLs**.
8. Enter the **Callback URL** you copied and click **Add URL**. If your organization does not show **MCP OAuth Redirect URLs**, ask Datadog support to allowlist the URL.

When you create the Access Policy that grants MCP Identity Gateway access to Datadog, use the **Select Existing** tab. Select the Credential Provider you saved here and click **Use Selected**. Do not create another Credential Provider: its **Callback URL** is not on the Datadog allowlist.

For details, see [Adding OAuth clients](https://docs.datadoghq.com/mcp_server/setup/#adding-oauth-clients) in the Datadog documentation.

## User authorization

Each user authorizes access to Datadog when they first connect through MCP Identity Gateway. The MCP User-Based Access Token Credential Provider has no administrator **Authorize** step.

## Create the Server Workload

1. Go to **Server Workloads** in the left sidebar and click **+ New**.

2. Configure the following fields, with your Datadog site’s MCP server host as the **Host**:

   | Field                     | Value                |
   | ------------------------- | -------------------- |
   | **Name**                  | A user-friendly name |
   | **Host**                  | `mcp.<datadog-site>` |
   | **Application Protocol**  | MCP                  |
   | **Port**                  | 443 with TLS         |
   | **URL Path**              | `/v1/mcp`            |
   | **Authentication method** | HTTP Authentication  |
   | **Authentication scheme** | Bearer               |

3. Click **Save**.

## Create an Access Policy

On the **Select Existing** tab, select the Credential Provider you saved during Datadog setup and click **Use Selected**. The linked setup procedure describes creating a new Credential Provider. Reuse yours, because Datadog allowlisted only its **Callback URL**.

This section creates the Gateway-to-Server Access Policy, which authorizes MCP Identity Gateway to access Datadog on behalf of authenticated users.

Create an Access Policy linking MCP Identity Gateway (as the Client Workload), the Credential Provider you created, and the Server Workload for Datadog.

> **The Gateway is the Client Workload**
>
> In this policy, the Client Workload is MCP Identity Gateway itself—not the AI agent or individual users. The AI agent connects through the separate Client-to-Gateway policy, and the Credential Provider enforces per-user access.

For step-by-step instructions, including the Client Workload settings that identify MCP Identity Gateway, see [Create the gateway-to-server Access Policy](../../../access-policies/mcp-identity-gateway/setup-mcp-gateway.md#create-the-gateway-to-server-access-policy).

## Verify

After a user authorizes access, the Aembit **AI Access Authorized** page lists the Datadog MCP Server as **Ready**. The AI agent can then call Datadog MCP tools through MCP Identity Gateway.
