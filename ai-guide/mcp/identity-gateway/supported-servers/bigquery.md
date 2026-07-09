---
type: how-to
title: "BigQuery MCP Server"
description: "Configure the BigQuery MCP Server to work with AI agents through the Aembit MCP Identity Gateway."
resource: https://docs.aembit.io/ai-guide/mcp/identity-gateway/supported-servers/bigquery/
interface: mcp
tags: [supported-server, identity-gateway, mcp]
timestamp: 2026-06-30T12:35:49-04:00
---

# BigQuery MCP Server

Aembit supports the official [BigQuery MCP server](https://docs.cloud.google.com/bigquery/docs/use-bigquery-mcp), which lets AI agents**AI Agent**: A software workload that authenticates to systems, requests credentials, and accesses resources, either on behalf of a person or on its own. Aembit secures AI agents with the same identity-first model it uses for any workload. User-driven agents such as Claude Desktop also carry a blended identity that ties access to both the user and the agent.[Learn more](../../../../get-started/use-cases/ai-agents.md) explore datasets and run SQL queries through MCP**Model Context Protocol**: A standard protocol for AI agent and server interactions that defines how AI assistants communicate with external tools and data sources.[Learn more(opens in new tab)](https://modelcontextprotocol.io/) tools.

This page describes how to configure BigQuery as an MCP server**MCP Server**: A server that implements the Model Context Protocol to provide tools, resources, or data to AI agents and MCP clients.[Learn more(opens in new tab)](https://modelcontextprotocol.io/specification/2025-03-26/server) behind the Aembit MCP Identity Gateway**MCP Identity Gateway**: A component that brokers MCP traffic between MCP clients and target MCP servers, validating authorization and presenting Aembit-managed credentials on each request.[Learn more](../overview.md). Each user authenticates with their own Google identity, and the Gateway injects their token into MCP requests.

Looking for direct BigQuery API access?

This page covers **MCP tool access through Aembit’s MCP Identity Gateway**. To broker credentials for the BigQuery REST API as a traditional Server Workload**Server Workload**: Server Workloads represent target services, APIs, databases, or applications that receive and respond to access requests from Client Workloads.[Learn more](../../../../get-started/concepts/server-workloads.md), see the [GCP BigQuery Server Workload guide](../../../../user-guide/access-policies/server-workloads/guides/gcp-bigquery.md) instead.

## Prerequisites

[Section titled “Prerequisites”](#prerequisites)

Before you begin, ensure you have the following:

* A Google Cloud project where you can enable APIs and create OAuth credentials
* A BigQuery dataset with data to query
* A configured [Aembit MCP Identity Gateway](../setup-mcp-gateway.md)

## Requirements and considerations

[Section titled “Requirements and considerations”](#requirements-and-considerations)

Before you configure BigQuery, review these requirements and behaviors specific to BigQuery’s MCP server.

* **Each user needs their own BigQuery access.** The Gateway authenticates with each user’s Google identity, so BigQuery enforces that user’s IAM roles rather than a shared service account. Also grant `roles/bigquery.jobUser`; without it, BigQuery rejects the `execute_sql` tool.
* **This guide grants read-only access.** It uses the `bigquery.readonly` scope, which covers browsing datasets and running queries. Add a broader scope if your agents need to modify data.

## Set up your Google Cloud Platform project

[Section titled “Set up your Google Cloud Platform project”](#set-up-your-google-cloud-platform-project)

Configure a Google Cloud project with the BigQuery API, user permissions, and an OAuth client.

1. In the [Google Cloud console](https://console.cloud.google.com/), select the project you want to use.

2. Enable the **BigQuery API** (`bigquery.googleapis.com`) for the project.

3. Grant each user who queries BigQuery the `roles/bigquery.dataViewer` and `roles/bigquery.jobUser` IAM roles.

4. Go to the [Credentials](https://console.cloud.google.com/apis/credentials) page, click **Create Credentials**, then select **OAuth client ID**. If your project has no consent screen, configure one first: choose a **User type**, enter the app details, then add the `https://www.googleapis.com/auth/bigquery.readonly` scope.

5. For **Application type**, select **Web application**, enter a name, then click **Create**. Leave **Authorized redirect URIs** empty for now. You add the Aembit Callback URL after you create the Credential Provider.

6. Copy the **Client ID** and **Client secret**, and store them for the next section.

## Configure the Credential Provider

[Section titled “Configure the Credential Provider”](#configure-the-credential-provider)

Create an MCP User-Based Access Token Credential Provider**Credential Provider**: Credential Providers obtain the specific access credentials—such as API keys, OAuth tokens, or temporary cloud credentials—that Client Workloads need to authenticate to Server Workloads.[Learn more](../../../../get-started/concepts/credential-providers.md) in Aembit.

1. Log into your Aembit Tenant.

2. Go to **Credential Providers** in the left sidebar and click **+ New**.

3. Configure the following fields:

   | Field               | Value                                               |
   | ------------------- | --------------------------------------------------- |
   | **Name**            | A user-friendly name                                |
   | **Credential Type** | MCP User-Based Access Token                         |
   | **MCP Server URL**  | `https://bigquery.googleapis.com/mcp`               |
   | **Client ID**       | The Client ID you copied from Google Cloud          |
   | **Client Secret**   | The Client Secret you copied from Google Cloud      |
   | **Scopes**          | `https://www.googleapis.com/auth/bigquery.readonly` |
   | **PKCE Required**   | On                                                  |

   For **MCP Server URL**, click **Discover** to populate the Authorization URL and Token URL.

4. Click **Save**.

5. Copy the read-only **Callback URL** from the Credential Provider.

## Finish configuring the OAuth client

[Section titled “Finish configuring the OAuth client”](#finish-configuring-the-oauth-client)

Return to the OAuth client in the Google Cloud console.

1. Open the **Web application** OAuth client you created.

2. Under **Authorized redirect URIs**, click **Add URI**, paste the Aembit **Callback URL**, then click **Save**.

## Authorize the Credential Provider

[Section titled “Authorize the Credential Provider”](#authorize-the-credential-provider)

1. Return to the Credential Provider in Aembit and click **Authorize**.

2. Choose your Google Account and approve access. The Credential Provider status changes to **Ready** when the flow completes.

## Create the Server Workload

[Section titled “Create the Server Workload”](#create-the-server-workload)

1. Go to **Server Workloads** in the left sidebar and click **+ New**.

2. Configure the following fields:

   | Field                     | Value                     |
   | ------------------------- | ------------------------- |
   | **Name**                  | A user-friendly name      |
   | **Host**                  | `bigquery.googleapis.com` |
   | **Application Protocol**  | MCP                       |
   | **Port**                  | 443 with TLS              |
   | **URL Path**              | `/mcp`                    |
   | **Authentication method** | HTTP Authentication       |
   | **Authentication scheme** | Bearer                    |

3. Click **Save**.

## Create an Access Policy

[Section titled “Create an Access Policy”](#create-an-access-policy)

Create an Access Policy**Access Policy**: Access Policies define, enforce, and audit access between Client and Server Workloads by cryptographically verifying workload identity and contextual factors rather than relying on static secrets.[Learn more](../../../../get-started/concepts/access-policies.md) linking your Client Workload**Client Workload**: Client Workloads represent software applications, scripts, or automated processes that initiate access requests to Server Workloads, operating autonomously without direct user interaction.[Learn more](../../../../get-started/concepts/client-workloads.md) (the AI agent), the MCP User-Based Access Token Credential Provider, and the BigQuery Server Workload. See [Access Policies](../../../../user-guide/access-policies/overview.md) for details.

## Verify

[Section titled “Verify”](#verify)

After a user authorizes access, the Aembit **AI Access Authorized** page lists the BigQuery MCP Server as **Ready**. The AI agent can then call BigQuery MCP tools (such as `execute_sql`) through the Gateway.
