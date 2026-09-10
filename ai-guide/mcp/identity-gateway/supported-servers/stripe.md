---
type: how-to
title: "Stripe MCP Server"
description: "Configure the Stripe MCP Server to work with AI agents through the Aembit MCP Identity Gateway."
resource: https://docs.aembit.io/ai-guide/mcp/identity-gateway/supported-servers/stripe/
interface: mcp
tags: ["supported-server", "identity-gateway", "mcp"]
timestamp: 2026-08-10T14:10:28-07:00
---

# Stripe MCP Server

Aembit supports the official [Stripe MCP server](https://docs.stripe.com/mcp), which lets AI agents query payments, billing, and account data through MCP tools.

This page describes how to configure Stripe as an MCP server behind the Aembit MCP Identity Gateway. Each user authenticates with their own Stripe identity, and the Gateway injects their token into MCP requests.

This guide builds the Gateway-to-Server Policy—the second of the two Access Policies the MCP Identity Gateway requires. You create the first, the Client-to-Gateway Policy, during [Gateway setup](../setup-mcp-gateway.md).

## Prerequisites

Before you begin, ensure you have the following:

* A Stripe account with MCP access enabled for the environment you plan to use
* A configured [Aembit MCP Identity Gateway](../setup-mcp-gateway.md)

## Requirements and considerations

Before you configure Stripe, review these requirements and behaviors specific to Stripe’s MCP server.

* **Use User-Based Auth.** Aembit supports User-Based Auth for Stripe.
* **An administrator enables MCP access per environment.** Turn on MCP access at [MCP settings](https://dashboard.stripe.com/settings/mcp). Sandbox and live mode each need their own approval, and an OAuth session covers only the environment the user authorized. Test mode works on a free account; live mode requires an activated Stripe business account.
* **Stripe’s MCP tools can write.** The `stripe_api_write` tool issues `POST`, `PATCH`, `PUT`, and `DELETE` calls, which covers actions such as issuing refunds and canceling subscriptions. Scope the Access Policy to the users who need that reach, and have the MCP client confirm tool calls with the user before running them.
* **Connected accounts can’t use OAuth.** A Connect platform that calls Stripe as one of its connected accounts must send a restricted API key with the `Stripe-Account` header, which the User-Based Auth flow doesn’t cover.

## Configure the Credential Provider

Create an MCP User-Based Access Token Credential Provider in Aembit.

1. Log into your Aembit Tenant.

2. Go to **Credential Providers** in the left sidebar and click **+ New**.

3. Configure the following fields:

   | Field               | Value                                     |
   | ------------------- | ----------------------------------------- |
   | **Name**            | A user-friendly name                      |
   | **Credential Type** | MCP User-Based Access Token               |
   | **MCP Server URL**  | `https://mcp.stripe.com`                  |
   | **Client ID**       | Auto-populated—no pre-registration needed |
   | **Client Secret**   | Auto-populated by Discover                |
   | **PKCE Required**   | On                                        |

   For **MCP Server URL**, click **Discover** to populate the Authorization URL and Token URL.

4. Click **Save**.

5. Copy the read-only **Callback URL** from the Credential Provider.

## Finish configuring the OAuth client

Stripe supports OAuth Dynamic Client Registration, so you don’t need to register an app in the Stripe Dashboard.

When you create the MCP User-Based Access Token Credential Provider and click **Discover**, Stripe fills in the client registration automatically—Client ID, Client Secret, and URLs. Stripe grants access when each user completes the OAuth consent flow in the browser.

## Authorize the Credential Provider

1. Return to the Credential Provider in Aembit and click **Authorize**.

2. Complete the Stripe sign-in, choose the account and environment to share, then approve access. The Credential Provider status changes to **Ready** when the flow completes.

## Create the Server Workload

1. Go to **Server Workloads** in the left sidebar and click **+ New**.

2. Configure the following fields:

   | Field                     | Value                |
   | ------------------------- | -------------------- |
   | **Name**                  | A user-friendly name |
   | **Host**                  | `mcp.stripe.com`     |
   | **Application Protocol**  | MCP                  |
   | **Port**                  | 443 with TLS         |
   | **URL Path**              | ``                   |
   | **Authentication method** | HTTP Authentication  |
   | **Authentication scheme** | Bearer               |

3. Click **Save**.

## Create an Access Policy

This section creates the Gateway-to-Server Access Policy, which authorizes the MCP Identity Gateway to access Stripe on behalf of authenticated users.

Create an Access Policy linking the MCP Identity Gateway (as the Client Workload), the Credential Provider you created, and the Server Workload for Stripe.

> **The Gateway is the Client Workload**
>
> In this policy, the Client Workload is the MCP Identity Gateway itself—not the AI agent or individual users. The AI agent connects through the separate Client-to-Gateway policy, and the Credential Provider enforces per-user access.

For step-by-step instructions, including the Client Workload settings that identify the Gateway, see [Create the gateway-to-server Access Policy](../setup-mcp-gateway.md#create-the-gateway-to-server-access-policy).

## Verify

After a user authorizes access, the Aembit **AI Access Authorized** page lists the Stripe MCP Server as **Ready**. The AI agent can then call Stripe MCP tools through the Gateway.
