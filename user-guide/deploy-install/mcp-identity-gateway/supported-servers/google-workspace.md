---
type: how-to
title: "Google Workspace MCP Servers"
description: "Configure the Google Workspace MCP Servers (Drive, Calendar, People, and Chat) to work with AI agents through the Aembit MCP Identity Gateway."
resource: https://docs.aembit.io/user-guide/deploy-install/mcp-identity-gateway/supported-servers/google-workspace/
interface: mcp
tags: ["supported-server", "mcp-identity-gateway", "deploy-install"]
timestamp: 2026-09-15T18:18:13-07:00
---

# Google Workspace MCP Servers

Aembit supports the official [Google Workspace MCP servers](https://developers.google.com/workspace/guides/configure-mcp-servers), which let AI agents read Drive files, calendar events, contacts, and Chat messages through MCP tools.

This page describes how to configure four Google Workspace MCP servers: Google Drive, Calendar, People, and Chat. The Aembit MCP Identity Gateway brokers the connection. Each user authenticates with their own Google identity, and the Gateway injects their token into MCP requests.

This guide builds the Gateway-to-Server Policy—the second of the two Access Policies the MCP Identity Gateway requires. You create the first, the Client-to-Gateway Policy, during [Gateway setup](../../../access-policies/mcp-identity-gateway/setup-mcp-gateway.md).

> **Looking for direct Google API access?**
>
> This page covers **MCP tool access through Aembit’s MCP Identity Gateway**. To broker credentials for a Google REST API as a traditional Server Workload, see the [Server Workload guides](../../../access-policies/server-workloads/guides/overview.md) instead.

## Prerequisites

Before you begin, ensure you have the following:

* A Google Cloud project where you can enable APIs and create OAuth credentials
* A Google Workspace organization enrolled in Google’s Developer Preview Program (see [Requirements and considerations](#requirements-and-considerations))
* A configured [Aembit MCP Identity Gateway](../../../access-policies/mcp-identity-gateway/setup-mcp-gateway.md)

## Requirements and considerations

Before you configure Google Workspace, review these requirements and behaviors specific to Google’s MCP servers.

* **Enroll in Google’s Developer Preview Program.** Google’s Workspace MCP servers are available only through the Developer Preview Program. Until your organization enrolls, Google returns permission errors for every tool call. Google notes that it may use data from preview APIs to train its AI models. Review Google’s Preview terms before you enroll.
* **Configure each service on its own.** Drive, Calendar, People, and Chat each require their own GCP OAuth client, their own enabled API, and their own Aembit Credential Provider, Server Workload, and Access Policy.
* **The People server uses a non-standard host.** Unlike the other servers, Google hosts the People MCP server directly at `people.googleapis.com`, not on a dedicated `*mcp.googleapis.com` subdomain.
* **Chat requires two enabled APIs and a Workspace account.** Chat needs both `chat.googleapis.com` and `chatmcp.googleapis.com` enabled on your GCP project, and it doesn’t work with a personal Google Account.
* **Gmail isn’t available.** This guide doesn’t cover Gmail.

## Service settings

Each service uses the same configuration flow but different values. Use the values for the service you’re configuring in the following steps. For Drive and Calendar, the scopes grant read and write access. Substitute the `.readonly` variant (for example, `https://www.googleapis.com/auth/drive.readonly` instead of `drive`) if your agents only need to read data. Chat uses read-only scopes except for `chat.messages.create`, which grants the ability to send messages. Omit that scope if your agents only need to read Chat data.

* Drive

  | Setting                  | Value                                    |
  | ------------------------ | ---------------------------------------- |
  | **OAuth scope**          | `https://www.googleapis.com/auth/drive`  |
  | **MCP Server URL**       | `https://drivemcp.googleapis.com/mcp/v1` |
  | **Server Workload host** | `drivemcp.googleapis.com`                |
  | **URL Path**             | `/mcp/v1`                                |

* Calendar

  | Setting                  | Value                                       |
  | ------------------------ | ------------------------------------------- |
  | **OAuth scope**          | `https://www.googleapis.com/auth/calendar`  |
  | **MCP Server URL**       | `https://calendarmcp.googleapis.com/mcp/v1` |
  | **Server Workload host** | `calendarmcp.googleapis.com`                |
  | **URL Path**             | `/mcp/v1`                                   |

* People

  | Setting                  | Value                                                                                                                                                   |
  | ------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
  | **OAuth scope**          | `https://www.googleapis.com/auth/contacts.readonly https://www.googleapis.com/auth/directory.readonly https://www.googleapis.com/auth/userinfo.profile` |
  | **MCP Server URL**       | `https://people.googleapis.com/mcp/v1`                                                                                                                  |
  | **Server Workload host** | `people.googleapis.com`                                                                                                                                 |
  | **URL Path**             | `/mcp/v1`                                                                                                                                               |

* Chat

  | Setting                  | Value                                                                                                                                                                                                                                                                                      |
  | ------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
  | **OAuth scope**          | `https://www.googleapis.com/auth/chat.spaces.readonly https://www.googleapis.com/auth/chat.memberships.readonly https://www.googleapis.com/auth/chat.messages.readonly https://www.googleapis.com/auth/chat.messages.create https://www.googleapis.com/auth/chat.users.readstate.readonly` |
  | **MCP Server URL**       | `https://chatmcp.googleapis.com/mcp/v1`                                                                                                                                                                                                                                                    |
  | **Server Workload host** | `chatmcp.googleapis.com`                                                                                                                                                                                                                                                                   |
  | **URL Path**             | `/mcp/v1`                                                                                                                                                                                                                                                                                  |

## Set up your Google Cloud Platform project

Repeat this section for each Google Workspace service you want to enable. Each service needs its own enabled API and its own OAuth client.

* Drive

  Google doesn’t support OAuth Dynamic Client Registration, so an administrator must create a GCP OAuth 2.0 client before users can authenticate.

  1. Enable the Drive API on your GCP project: `gcloud services enable drive.googleapis.com --project=<YOUR_PROJECT>`
  2. In the GCP Console, go to **APIs & Services > OAuth consent screen**. Choose **Internal** for same-org users, or **External** for cross-org testing. Under **Scopes**, add `https://www.googleapis.com/auth/drive`. For read-only access, use `https://www.googleapis.com/auth/drive.readonly` instead.
  3. Go to **APIs & Services > Credentials**, click **Create Credentials > OAuth 2.0 Client ID**, and select **Web application** as the application type. Give it any name. You add the Aembit Callback URL under **Authorized redirect URIs** after you create the Credential Provider.
  4. Copy the **Client ID** and **Client Secret**, and store them for the Credential Provider configuration.

* Calendar

  Google doesn’t support OAuth Dynamic Client Registration, so an administrator must create a GCP OAuth 2.0 client before users can authenticate.

  1. Enable the Calendar API on your GCP project: `gcloud services enable calendar-json.googleapis.com --project=<YOUR_PROJECT>`
  2. In the GCP Console, go to **APIs & Services > OAuth consent screen**. Choose **Internal** for same-org users, or **External** for cross-org testing. Under **Scopes**, add `https://www.googleapis.com/auth/calendar`. For read-only access, use `https://www.googleapis.com/auth/calendar.readonly` instead.
  3. Go to **APIs & Services > Credentials**, click **Create Credentials > OAuth 2.0 Client ID**, and select **Web application** as the application type. Give it any name. You add the Aembit Callback URL under **Authorized redirect URIs** after you create the Credential Provider.
  4. Copy the **Client ID** and **Client Secret**, and store them for the Credential Provider configuration.

* People

  Google doesn’t support OAuth Dynamic Client Registration, so an administrator must create a GCP OAuth 2.0 client before users can authenticate.

  1. Enable the People API on your GCP project: `gcloud services enable people.googleapis.com --project=<YOUR_PROJECT>`
  2. In the GCP Console, go to **APIs & Services > OAuth consent screen**. Choose **Internal** for same-org users, or **External** for cross-org testing. Under **Scopes**, add the three scopes this server uses: `contacts.readonly` (the user’s personal contacts), `directory.readonly` (the Workspace directory of all users in the organization), and `userinfo.profile` (the authenticated user’s own profile).
  3. Go to **APIs & Services > Credentials**, click **Create Credentials > OAuth 2.0 Client ID**, and select **Web application** as the application type. Give it any name. You add the Aembit Callback URL under **Authorized redirect URIs** after you create the Credential Provider.
  4. Copy the **Client ID** and **Client Secret**, and store them for the Credential Provider configuration.

* Chat

  Google doesn’t support OAuth Dynamic Client Registration, so an administrator must create a GCP OAuth 2.0 client before users can authenticate.

  1. Enable both APIs that Chat requires on your GCP project: `gcloud services enable chat.googleapis.com chatmcp.googleapis.com --project=<YOUR_PROJECT>`

  2. In the GCP Console, go to **APIs & Services > OAuth consent screen**. Choose **Internal** for same-org users, or **External** for cross-org testing. Under **Scopes**, add all five scopes Chat requires:

     * `https://www.googleapis.com/auth/chat.spaces.readonly`
     * `https://www.googleapis.com/auth/chat.memberships.readonly`
     * `https://www.googleapis.com/auth/chat.messages.readonly`
     * `https://www.googleapis.com/auth/chat.messages.create`
     * `https://www.googleapis.com/auth/chat.users.readstate.readonly`

     If your agents only need to read Chat data, omit `chat.messages.create`, which grants the ability to send messages.

  3. Go to **APIs & Services > Credentials**, click **Create Credentials > OAuth 2.0 Client ID**, and select **Web application** as the application type. Give it any name. You add the Aembit Callback URL under **Authorized redirect URIs** after you create the Credential Provider.

  4. Copy the **Client ID** and **Client Secret**, and store them for the Credential Provider configuration.

## Configure the Credential Provider

Create an MCP User-Based Access Token Credential Provider in Aembit for the service.

1. Log into your Aembit Tenant.

2. Go to **Credential Providers** in the left sidebar and click **+ New**.

3. Configure the following fields:

   | Field               | Value                                                                     |
   | ------------------- | ------------------------------------------------------------------------- |
   | **Name**            | A user-friendly name                                                      |
   | **Credential Type** | MCP User-Based Access Token                                               |
   | **MCP Server URL**  | Your service’s MCP Server URL (see [Service settings](#service-settings)) |
   | **Client ID**       | The Client ID you copied from Google Cloud                                |
   | **Client Secret**   | The Client Secret you copied from Google Cloud                            |
   | **Scopes**          | Your service’s OAuth scope (see [Service settings](#service-settings))    |
   | **PKCE Required**   | On                                                                        |

   For **MCP Server URL**, click **Discover** to populate the Authorization URL and Token URL.

4. Click **Save**.

5. Copy the read-only **Callback URL** from the Credential Provider.

## Finish configuring the OAuth client

* Drive

  After you create the Credential Provider, copy its read-only **Callback URL** and return to the GCP OAuth 2.0 Client ID you created in **APIs & Services > Credentials**.

  1. Open the OAuth 2.0 Client ID, and under **Authorized redirect URIs** click **Add URI**, paste the Aembit **Callback URL**, then click **Save**.
  2. Confirm the Credential Provider scope matches the scope you added on the OAuth consent screen: `https://www.googleapis.com/auth/drive`

* Calendar

  After you create the Credential Provider, copy its read-only **Callback URL** and return to the GCP OAuth 2.0 Client ID you created in **APIs & Services > Credentials**.

  1. Open the OAuth 2.0 Client ID, and under **Authorized redirect URIs** click **Add URI**, paste the Aembit **Callback URL**, then click **Save**.
  2. Confirm the Credential Provider scope matches the scope you added on the OAuth consent screen: `https://www.googleapis.com/auth/calendar`

* People

  After you create the Credential Provider, copy its read-only **Callback URL** and return to the GCP OAuth 2.0 Client ID you created in **APIs & Services > Credentials**.

  1. Open the OAuth 2.0 Client ID, and under **Authorized redirect URIs** click **Add URI**, paste the Aembit **Callback URL**, then click **Save**.
  2. Confirm the Credential Provider scopes match the scopes you added on the OAuth consent screen: `https://www.googleapis.com/auth/contacts.readonly https://www.googleapis.com/auth/directory.readonly https://www.googleapis.com/auth/userinfo.profile`

* Chat

  After you create the Credential Provider, copy its read-only **Callback URL** and return to the GCP OAuth 2.0 Client ID you created in **APIs & Services > Credentials**.

  1. Open the OAuth 2.0 Client ID, and under **Authorized redirect URIs** click **Add URI**, paste the Aembit **Callback URL**, then click **Save**.
  2. Confirm the Credential Provider scopes match the five Chat scopes you added on the OAuth consent screen.

## Authorize the Credential Provider

1. Return to the Credential Provider in Aembit and click **Authorize**.

2. Choose your Google Account and approve access. The Credential Provider status changes to **Ready** when the flow completes.

## Create the Server Workload

1. Go to **Server Workloads** in the left sidebar and click **+ New**.

2. Configure the following fields:

   | Field                     | Value                                                                           |
   | ------------------------- | ------------------------------------------------------------------------------- |
   | **Name**                  | A user-friendly name                                                            |
   | **Host**                  | Your service’s Server Workload host (see [Service settings](#service-settings)) |
   | **Application Protocol**  | MCP                                                                             |
   | **Port**                  | 443 with TLS                                                                    |
   | **URL Path**              | Your service’s URL Path (see [Service settings](#service-settings))             |
   | **Authentication method** | HTTP Authentication                                                             |
   | **Authentication scheme** | Bearer                                                                          |

3. Click **Save**.

## Create an Access Policy

This section creates the Gateway-to-Server Access Policy, which authorizes the MCP Identity Gateway to access each Google Workspace service on behalf of authenticated users.

Create an Access Policy linking the MCP Identity Gateway (as the Client Workload), the Credential Provider you created, and the Server Workload for each Google Workspace service.

> **The Gateway is the Client Workload**
>
> In this policy, the Client Workload is the MCP Identity Gateway itself—not the AI agent or individual users. The AI agent connects through the separate Client-to-Gateway policy, and the Credential Provider enforces per-user access.

For step-by-step instructions, including the Client Workload settings that identify the Gateway, see [Create the gateway-to-server Access Policy](../../../access-policies/mcp-identity-gateway/setup-mcp-gateway.md#create-the-gateway-to-server-access-policy).

## Verify

After a user authorizes access, the Aembit **AI Access Authorized** page lists each configured Google Workspace MCP Server as **Ready**. The AI agent can then call the corresponding MCP tools (such as listing Drive files or calendar events) through the Gateway.
