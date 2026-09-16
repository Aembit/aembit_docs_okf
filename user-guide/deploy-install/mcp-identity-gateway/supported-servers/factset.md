---
type: how-to
title: "FactSet MCP Server"
description: "Configure the FactSet MCP Server to work with AI agents through the Aembit MCP Identity Gateway."
resource: https://docs.aembit.io/user-guide/deploy-install/mcp-identity-gateway/supported-servers/factset/
interface: mcp
tags: ["supported-server", "mcp-identity-gateway", "deploy-install"]
timestamp: 2026-09-15T18:18:13-07:00
---

# FactSet MCP Server

Aembit supports the official [FactSet AI-ready data MCP server](https://developer.factset.com/mcp/factset-ai-ready-data-mcp), which lets AI agents query FactSet AI-ready financial data through MCP tools.

This page describes how to configure FactSet as an MCP server behind the Aembit MCP Identity Gateway.

This guide builds the Gateway-to-Server Policy—the second of the two Access Policies the MCP Identity Gateway requires. You create the first, the Client-to-Gateway Policy, during [Gateway setup](../../../access-policies/mcp-identity-gateway/setup-mcp-gateway.md).

## Prerequisites

Before you begin, ensure you have the following:

* A FactSet account provisioned by FactSet (self-signup isn’t available)
* A configured [Aembit MCP Identity Gateway](../../../access-policies/mcp-identity-gateway/setup-mcp-gateway.md)

## Requirements and considerations

Before you configure FactSet, review these requirements and behaviors specific to FactSet’s MCP server.

* **No `offline_access` scope needed.** FactSet issues refresh tokens without the `offline_access` scope—don’t add it.
* **Public client, no secret for Admin-Based Auth.** The FactSet developer-portal registration produces a public client; leave the **Client Secret** field empty in the Admin-Based Credential Provider. (User-Based Auth is unaffected—Discover fills in its own client registration.)

## Choose an authentication approach

Aembit supports two authentication approaches for FactSet. The tabs in the following sections stay in sync with the approach you choose.

* **User-Based**: each user authenticates with their own FactSet identity, and the Gateway injects that user’s token into MCP requests. Aembit stores and refreshes each user’s tokens individually.
* **Admin-Based**: an administrator completes the OAuth flow once during setup, and all users who access FactSet through the Gateway share the resulting tokens.

## Configure the Credential Provider

* User-Based

  Create an MCP User-Based Access Token Credential Provider in Aembit.

  1. Log into your Aembit Tenant.

  2. Go to **Credential Providers** in the left sidebar and click **+ New**.

  3. Configure the following fields:

     | Field               | Value                                     |
     | ------------------- | ----------------------------------------- |
     | **Name**            | A user-friendly name                      |
     | **Credential Type** | MCP User-Based Access Token               |
     | **MCP Server URL**  | `https://mcp.factset.com/content/v1`      |
     | **Client ID**       | Auto-populated—no pre-registration needed |
     | **Client Secret**   | Auto-populated by Discover                |
     | **PKCE Required**   | On                                        |
     | **Lifetime**        | `1 year`                                  |

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
     | **Scopes**          | `openid mcp email`                                     |
     | **OAuth URL**       | `https://auth.factset.com`                             |
     | **PKCE Required**   | On                                                     |
     | **Lifetime**        | `1 year`                                               |

     For **OAuth URL**, click **URL Discovery** to populate the Authorization URL and Token URL.

  4. Click **Save**.

  5. Copy the read-only **Callback URL** from the Credential Provider.

## Register the OAuth client

* User-Based

  FactSet supports OAuth Dynamic Client Registration, so you don’t need to register a vendor app.

  When you create the MCP User-Based Access Token Credential Provider and click **Discover**, FactSet fills in the client registration automatically—Client ID, Client Secret, scopes, and URLs. FactSet grants access through OAuth consent the first time a user authenticates.

* Admin-Based

  Admin-Based Auth registers an app on the FactSet developer portal. FactSet issues a public client, so the Client Secret stays empty.

  1. Create the MCP Credential Provider in Aembit first, and copy its read-only **Callback URL**.
  2. Go to the [FactSet AI-Ready Data MCP page](https://developer.factset.com/mcp/factset-ai-ready-data-mcp).
  3. Under **Add to your Client**, select **Proprietary MCP Client**.
  4. Follow **Step 2** on that page and enter the Aembit Credential Provider’s **Callback URL** as the **Redirect URI**.
  5. Save the app. The response JSON includes a `clientId`—copy it into the Credential Provider. FactSet issues a public client, so leave the **Client Secret** empty.

## Authorize the Credential Provider

* User-Based

  There’s no administrator authorization step for this credential type. Each user completes their own FactSet sign-in and consent the first time they access FactSet through the Gateway, and Aembit stores and refreshes each user’s tokens individually.

* Admin-Based

  1. Return to the Credential Provider in Aembit and click **Authorize**.

  2. Complete the FactSet sign-in as an administrator and approve access. The Credential Provider status changes to **Ready** when the flow completes.

## Create the Server Workload

1. Go to **Server Workloads** in the left sidebar and click **+ New**.

2. Configure the following fields:

   | Field                     | Value                |
   | ------------------------- | -------------------- |
   | **Name**                  | A user-friendly name |
   | **Host**                  | `mcp.factset.com`    |
   | **Application Protocol**  | MCP                  |
   | **Port**                  | 443 with TLS         |
   | **URL Path**              | `/content/v1`        |
   | **Authentication method** | HTTP Authentication  |
   | **Authentication scheme** | Bearer               |

3. Click **Save**.

## Create an Access Policy

This section creates the Gateway-to-Server Access Policy, which authorizes the MCP Identity Gateway to access FactSet on behalf of authenticated users.

Create an Access Policy linking the MCP Identity Gateway (as the Client Workload), the Credential Provider you created, and the Server Workload for FactSet.

> **The Gateway is the Client Workload**
>
> In this policy, the Client Workload is the MCP Identity Gateway itself—not the AI agent or individual users. The AI agent connects through the separate Client-to-Gateway policy, and the Credential Provider enforces per-user access.

For step-by-step instructions, including the Client Workload settings that identify the Gateway, see [Create the gateway-to-server Access Policy](../../../access-policies/mcp-identity-gateway/setup-mcp-gateway.md#create-the-gateway-to-server-access-policy).

## Verify

After authorization completes, the Aembit **AI Access Authorized** page lists the FactSet MCP Server as **Ready**. The AI agent can then call FactSet MCP tools through the Gateway.
