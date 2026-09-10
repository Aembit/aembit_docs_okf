---
type: how-to
title: "Salesforce MCP Server"
description: "Configure the Salesforce MCP Server to work with AI agents through the Aembit MCP Identity Gateway."
resource: https://docs.aembit.io/ai-guide/mcp/identity-gateway/supported-servers/salesforce/
interface: mcp
tags: ["supported-server", "identity-gateway", "mcp"]
timestamp: 2026-08-11T08:52:51-07:00
---

# Salesforce MCP Server

Aembit supports the official [Salesforce-hosted MCP servers](https://developer.salesforce.com/docs/platform/hosted-mcp-servers/guide/setup-overview.html), which let AI agents access Salesforce data and actions through MCP tools.

This page describes how to configure Salesforce as an MCP server behind the Aembit MCP Identity Gateway.

This guide builds the Gateway-to-Server Policy—the second of the two Access Policies the MCP Identity Gateway requires. You create the first, the Client-to-Gateway Policy, during [Gateway setup](../setup-mcp-gateway.md).

> **Looking for direct Salesforce API access?**
>
> This page covers **MCP tool access through Aembit’s MCP Identity Gateway**. To broker credentials for Salesforce’s REST API as a traditional Server Workload, see the [Salesforce Server Workload guide](../../../../user-guide/access-policies/server-workloads/guides/salesforce-rest.md) instead.

## Prerequisites

Before you begin, ensure you have the following:

* A Salesforce org, Developer Edition or higher (Developer Edition organizations include free access to hosted MCP servers)
* A configured [Aembit MCP Identity Gateway](../setup-mcp-gateway.md)

## Requirements and considerations

Before you configure Salesforce, review these requirements and behaviors specific to Salesforce’s MCP server.

* **No Dynamic Client Registration.** Both authentication approaches need the manually created External Client App.
* **Org-specific OAuth URL.** The Discover URL is your Salesforce org URL (for example, `https://orgname.develop.my.salesforce.com/`), not a fixed global URL.
* **Multiple MCP servers.** Salesforce exposes many MCP servers with different access levels; each needs its own Server Workload in Aembit, since each uses a different path. The full list is in Setup > MCP Servers.
* **Activation delay.** A new External Client App can take up to 30 minutes to become active.
* **Reauthorize after scope changes.** Editing the scopes on an existing Credential Provider requires immediate reauthorization—the old token won’t reflect the new scopes.
* **Activate the MCP server first.** Activate each MCP server in **Setup > MCP Servers** before use.

## Choose an authentication approach

Aembit supports two authentication approaches for Salesforce. The tabs in the following sections stay in sync with the approach you choose.

* **User-Based**: each user authenticates with their own Salesforce identity, and the Gateway injects that user’s token into MCP requests. Aembit stores and refreshes each user’s tokens individually.
* **Admin-Based**: an administrator completes the OAuth flow once during setup, and all users who access Salesforce through the Gateway share the resulting tokens.

## Create the external client app

Salesforce doesn’t support OAuth Dynamic Client Registration, so an administrator must create one External Client App before users can authenticate. Both authentication approaches share this app: the **Consumer Key** is the Client ID and the **Consumer Secret** is the Client Secret. This setup is once per Salesforce org.

1. In Setup, search for **External Client App Manager**, then click **New External Client App**.

2. Name the app, expand **API (Enable OAuth Settings)**, and check **Enable OAuth**.

3. Set the **Callback URL** to the Aembit Credential Provider’s **Callback URL**. Copy the exact read-only value from the Credential Provider after you create it in Aembit.

4. Add these OAuth scopes:

   * `mcp_api` (Access Salesforce-hosted MCP Servers)
   * `refresh_token` / `offline_access` (Perform requests at any time)
   * `api` (Manage user data via APIs)
   * `sfap_api` (Access the Salesforce API Platform)

5. Under **Security**, enable both of these settings:

   * **Require Proof Key for Code Exchange (PKCE) extension for Supported Authorization Flows**
   * **Issue JSON Web Token (JWT)-based access tokens for named users**

6. Click **Create**, then retrieve the **Consumer Key** (Client ID) and **Consumer Secret** (Client Secret) from **Settings > Consumer Key and Secret**.

7. In Setup, go to **MCP Servers**, select the MCP server you want, and click **Activate**.

A new External Client App can take up to 30 minutes to become active.

## Configure the Credential Provider

* User-Based

  Create an MCP User-Based Access Token Credential Provider in Aembit.

  1. Log into your Aembit Tenant.

  2. Go to **Credential Providers** in the left sidebar and click **+ New**.

  3. Configure the following fields:

     | Field               | Value                                               |
     | ------------------- | --------------------------------------------------- |
     | **Name**            | A user-friendly name                                |
     | **Credential Type** | MCP User-Based Access Token                         |
     | **MCP Server URL**  | `https://<your-salesforce-org>.salesforce.com/`     |
     | **Client ID**       | The Client ID you copied earlier                    |
     | **Client Secret**   | The Client Secret you copied earlier                |
     | **Scopes**          | `refresh_token offline_access mcp_api api sfap_api` |
     | **PKCE Required**   | On                                                  |
     | **Lifetime**        | `1 year`                                            |

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
     | **Scopes**          | `refresh_token offline_access mcp_api api sfap_api`    |
     | **OAuth URL**       | `https://<your-salesforce-org>.salesforce.com/`        |
     | **PKCE Required**   | On                                                     |
     | **Lifetime**        | `1 year`                                               |

     For **OAuth URL**, click **URL Discovery** to populate the Authorization URL and Token URL.

  4. Click **Save**.

  5. Copy the read-only **Callback URL** from the Credential Provider.

## Finish configuring the external client app

* User-Based

  User-Based Auth uses the shared External Client App you create in the prerequisites—both authentication approaches use the same app.

  1. Create the MCP User-Based Access Token Credential Provider in Aembit and copy its read-only **Callback URL**.
  2. Confirm the External Client App’s **Callback URL** setting includes that Callback URL (see the prerequisites).
  3. Supply the app’s **Consumer Key** as the Client ID and its **Consumer Secret** as the Client Secret in the Credential Provider.

* Admin-Based

  Admin-Based Auth uses the same shared External Client App you create in the prerequisites—both authentication approaches use the same app.

  1. Create the MCP Credential Provider (OAuth 2.0 Authorization Code) in Aembit and copy its read-only **Callback URL**.
  2. Confirm the External Client App’s **Callback URL** setting includes that Callback URL (see the prerequisites).
  3. Supply the app’s **Consumer Key** as the Client ID and its **Consumer Secret** as the Client Secret in the Credential Provider.

## Authorize the Credential Provider

* User-Based

  There’s no administrator authorization step for this credential type. Each user completes their own Salesforce sign-in and consent the first time they access Salesforce through the Gateway, and Aembit stores and refreshes each user’s tokens individually.

* Admin-Based

  1. Return to the Credential Provider in Aembit and click **Authorize**.

  2. Complete the Salesforce sign-in as an administrator and approve access. The Credential Provider status changes to **Ready** when the flow completes.

## Create the Server Workload

1. Go to **Server Workloads** in the left sidebar and click **+ New**.

2. Configure the following fields:

   | Field                     | Value                                         |
   | ------------------------- | --------------------------------------------- |
   | **Name**                  | A user-friendly name                          |
   | **Host**                  | `api.salesforce.com`                          |
   | **Application Protocol**  | MCP                                           |
   | **Port**                  | 443 with TLS                                  |
   | **URL Path**              | `/platform/mcp/v1/platform/<mcp-server-name>` |
   | **Authentication method** | HTTP Authentication                           |
   | **Authentication scheme** | Bearer                                        |

3. Click **Save**.

## Create an Access Policy

This section creates the Gateway-to-Server Access Policy, which authorizes the MCP Identity Gateway to access Salesforce on behalf of authenticated users.

Create an Access Policy linking the MCP Identity Gateway (as the Client Workload), the Credential Provider you created, and the Server Workload for Salesforce.

> **The Gateway is the Client Workload**
>
> In this policy, the Client Workload is the MCP Identity Gateway itself—not the AI agent or individual users. The AI agent connects through the separate Client-to-Gateway policy, and the Credential Provider enforces per-user access.

For step-by-step instructions, including the Client Workload settings that identify the Gateway, see [Create the gateway-to-server Access Policy](../setup-mcp-gateway.md#create-the-gateway-to-server-access-policy).

## Verify

After authorization completes, the Aembit **AI Access Authorized** page lists the Salesforce MCP Server as **Ready**. The AI agent can then call Salesforce MCP tools through the Gateway.
