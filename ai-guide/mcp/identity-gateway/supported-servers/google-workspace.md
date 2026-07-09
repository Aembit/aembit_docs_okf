---
type: how-to
title: "Google Workspace MCP Servers"
description: "Configure the Google Workspace MCP Servers (Drive, Calendar, and People) to work with AI agents through the Aembit MCP Identity Gateway."
resource: https://docs.aembit.io/ai-guide/mcp/identity-gateway/supported-servers/google-workspace/
interface: mcp
tags: [supported-server, identity-gateway, mcp]
timestamp: 2026-06-30T12:35:49-04:00
---

# Google Workspace MCP Servers

Aembit supports the official [Google Workspace MCP servers](https://developers.google.com/workspace/guides/configure-mcp-servers), which let AI agents**AI Agent**: A software workload that authenticates to systems, requests credentials, and accesses resources, either on behalf of a person or on its own. Aembit secures AI agents with the same identity-first model it uses for any workload. User-driven agents such as Claude Desktop also carry a blended identity that ties access to both the user and the agent.[Learn more](../../../../get-started/use-cases/ai-agents.md) read Drive files, calendar events, and contacts through MCP**Model Context Protocol**: A standard protocol for AI agent and server interactions that defines how AI assistants communicate with external tools and data sources.[Learn more(opens in new tab)](https://modelcontextprotocol.io/) tools.

This page describes how to configure three Google Workspace MCP servers**MCP Server**: A server that implements the Model Context Protocol to provide tools, resources, or data to AI agents and MCP clients.[Learn more(opens in new tab)](https://modelcontextprotocol.io/specification/2025-03-26/server): Google Drive, Calendar, and People. The Aembit MCP Identity Gateway**MCP Identity Gateway**: A component that brokers MCP traffic between MCP clients and target MCP servers, validating authorization and presenting Aembit-managed credentials on each request.[Learn more](../overview.md) brokers the connection. Each user authenticates with their own Google identity, and the Gateway injects their token into MCP requests.

Looking for direct Google API access?

This page covers **MCP tool access through Aembit’s MCP Identity Gateway**. To broker credentials for a Google REST API as a traditional Server Workload**Server Workload**: Server Workloads represent target services, APIs, databases, or applications that receive and respond to access requests from Client Workloads.[Learn more](../../../../get-started/concepts/server-workloads.md), see the [Server Workload guides](../../../../user-guide/access-policies/server-workloads/guides/overview.md) instead.

## Prerequisites

[Section titled “Prerequisites”](#prerequisites)

Before you begin, ensure you have the following:

* A Google Cloud project where you can enable APIs and create OAuth credentials
* A Google Workspace organization enrolled in Google’s Developer Preview Program (see [Requirements and considerations](#requirements-and-considerations))
* A configured [Aembit MCP Identity Gateway](../setup-mcp-gateway.md)

## Requirements and considerations

[Section titled “Requirements and considerations”](#requirements-and-considerations)

Before you configure Google Workspace, review these requirements and behaviors specific to Google’s MCP servers.

* **Enroll in Google’s Developer Preview Program.** Google’s Workspace MCP servers are available only through the Developer Preview Program. Until your organization enrolls, Google returns permission errors for every tool call. Preview enrollment also expands the data Google collects on the vendor side. Review Google’s Preview terms before you enroll.
* **Configure each service on its own.** Drive, Calendar, and People each require their own GCP OAuth client, their own enabled API, and their own Aembit Credential Provider, Server Workload, and Access Policy.
* **Google Chat isn’t available.** The Gateway returns an “Internal error encountered” for every Google Chat tool, so this guide doesn’t cover Chat.
* **Gmail isn’t available.** This guide doesn’t cover Gmail.

## Service settings

[Section titled “Service settings”](#service-settings)

Each service uses the same configuration flow but different values. Use the values for the service you’re configuring in the following steps. These scopes grant read-only access. Substitute the read/write variant (for example, `https://www.googleapis.com/auth/drive` instead of `drive.readonly`) if your agents need to modify data.

* Drive

  | Setting                  | Value                                            |
  | ------------------------ | ------------------------------------------------ |
  | **API to enable**        | `drive.googleapis.com`                           |
  | **OAuth scope**          | `https://www.googleapis.com/auth/drive.readonly` |
  | **MCP Server URL**       | `https://drivemcp.googleapis.com/mcp/v1`         |
  | **Server Workload host** | `drivemcp.googleapis.com`                        |
  | **URL Path**             | `/mcp/v1`                                        |

* Calendar

  | Setting                  | Value                                               |
  | ------------------------ | --------------------------------------------------- |
  | **API to enable**        | `calendar-json.googleapis.com`                      |
  | **OAuth scope**          | `https://www.googleapis.com/auth/calendar.readonly` |
  | **MCP Server URL**       | `https://calendarmcp.googleapis.com/mcp/v1`         |
  | **Server Workload host** | `calendarmcp.googleapis.com`                        |
  | **URL Path**             | `/mcp/v1`                                           |

* People

  | Setting                  | Value                                                                                                                                                   |
  | ------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
  | **API to enable**        | `people.googleapis.com`                                                                                                                                 |
  | **OAuth scope**          | `https://www.googleapis.com/auth/contacts.readonly https://www.googleapis.com/auth/directory.readonly https://www.googleapis.com/auth/userinfo.profile` |
  | **MCP Server URL**       | `https://people.googleapis.com/mcp/v1`                                                                                                                  |
  | **Server Workload host** | `people.googleapis.com`                                                                                                                                 |
  | **URL Path**             | `/mcp/v1`                                                                                                                                               |

## Set up your Google Cloud Platform project

[Section titled “Set up your Google Cloud Platform project”](#set-up-your-google-cloud-platform-project)

Repeat this section for each Google Workspace service you want to enable. Each service needs its own enabled API and its own OAuth client.

1. In the [Google Cloud console](https://console.cloud.google.com/), select the project you want to use.

2. Enable the API for your service (see [Service settings](#service-settings)). If a later tool call reports that an API “has not been used or is disabled,” enable the API named in that error too.

3. Go to the [Credentials](https://console.cloud.google.com/apis/credentials) page, click **Create Credentials**, then select **OAuth client ID**. If your project has no consent screen, configure one first: choose a **User type**, enter the app details, then add your service’s OAuth scope.

4. For **Application type**, select **Web application**, enter a name, then click **Create**. Leave **Authorized redirect URIs** empty for now. You add the Aembit Callback URL after you create the Credential Provider.

5. Copy the **Client ID** and **Client secret**, and store them for the next section.

## Configure the Credential Provider

[Section titled “Configure the Credential Provider”](#configure-the-credential-provider)

Create an MCP User-Based Access Token Credential Provider**Credential Provider**: Credential Providers obtain the specific access credentials—such as API keys, OAuth tokens, or temporary cloud credentials—that Client Workloads need to authenticate to Server Workloads.[Learn more](../../../../get-started/concepts/credential-providers.md) in Aembit for the service.

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

[Section titled “Finish configuring the OAuth client”](#finish-configuring-the-oauth-client)

Return to the OAuth client in the Google Cloud console.

1. Open the **Web application** OAuth client you created for this service.

2. Under **Authorized redirect URIs**, click **Add URI**, paste the Aembit **Callback URL**, then click **Save**.

## Authorize the Credential Provider

[Section titled “Authorize the Credential Provider”](#authorize-the-credential-provider)

1. Return to the Credential Provider in Aembit and click **Authorize**.

2. Choose your Google Account and approve access. The Credential Provider status changes to **Ready** when the flow completes.

## Create the Server Workload

[Section titled “Create the Server Workload”](#create-the-server-workload)

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

[Section titled “Create an Access Policy”](#create-an-access-policy)

Create an Access Policy**Access Policy**: Access Policies define, enforce, and audit access between Client and Server Workloads by cryptographically verifying workload identity and contextual factors rather than relying on static secrets.[Learn more](../../../../get-started/concepts/access-policies.md) linking your Client Workload**Client Workload**: Client Workloads represent software applications, scripts, or automated processes that initiate access requests to Server Workloads, operating autonomously without direct user interaction.[Learn more](../../../../get-started/concepts/client-workloads.md) (the AI agent), the service’s MCP User-Based Access Token Credential Provider, and the service’s Server Workload. See [Access Policies](../../../../user-guide/access-policies/overview.md) for details.

## Verify

[Section titled “Verify”](#verify)

After a user authorizes access, the Aembit **AI Access Authorized** page lists each configured Google Workspace MCP Server as **Ready**. The AI agent can then call the corresponding MCP tools (such as listing Drive files or calendar events) through the Gateway.
