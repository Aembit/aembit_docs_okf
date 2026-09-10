---
type: how-to
title: "BigQuery MCP Server"
description: "Configure the BigQuery MCP Server to work with AI agents through the Aembit MCP Identity Gateway."
resource: https://docs.aembit.io/ai-guide/mcp/identity-gateway/supported-servers/bigquery/
interface: mcp
tags: ["supported-server", "identity-gateway", "mcp"]
timestamp: 2026-08-10T14:01:53-07:00
---

# BigQuery MCP Server

Aembit supports the official [BigQuery MCP server](https://docs.cloud.google.com/bigquery/docs/use-bigquery-mcp), which lets AI agents explore datasets and run SQL queries through MCP tools.

This page describes how to configure BigQuery as an MCP server behind the Aembit MCP Identity Gateway. Each user authenticates with their own Google identity, and the Gateway injects their token into MCP requests.

This guide builds the Gateway-to-Server Policy—the second of the two Access Policies the MCP Identity Gateway requires. You create the first, the Client-to-Gateway Policy, during [Gateway setup](../setup-mcp-gateway.md).

> **Looking for direct BigQuery API access?**
>
> This page covers **MCP tool access through Aembit’s MCP Identity Gateway**. To broker credentials for the BigQuery REST API as a traditional Server Workload, see the [GCP BigQuery Server Workload guide](../../../../user-guide/access-policies/server-workloads/guides/gcp-bigquery.md) instead.

## Prerequisites

Before you begin, ensure you have the following:

* A Google Cloud project where you can enable APIs and create OAuth credentials
* A BigQuery dataset with data to query
* A configured [Aembit MCP Identity Gateway](../setup-mcp-gateway.md)

## Requirements and considerations

Before you configure BigQuery, review these requirements and behaviors specific to BigQuery’s MCP server.

* **Non-standard MCP path.** BigQuery uses `/mcp`, not the `/mcp/v1` path of the other Google MCP servers. Use `https://bigquery.googleapis.com/mcp` as the MCP Server URL.
* **IAM roles required.** The OAuth scope alone isn’t sufficient. Users without `roles/bigquery.dataViewer` and `roles/bigquery.jobUser` authenticate successfully but get permission errors on tool calls. Grant both roles on the GCP project or dataset.
* **Use User-Based Auth.** Aembit supports User-Based Auth for Google BigQuery.

## Set up your Google Cloud Platform project

Google doesn’t support OAuth Dynamic Client Registration, so an administrator must create a GCP OAuth 2.0 client before users can authenticate.

1. Enable the BigQuery API on your GCP project: `gcloud services enable bigquery.googleapis.com --project=<YOUR_PROJECT>`
2. In the GCP Console, go to **APIs & Services > OAuth consent screen**. Choose **Internal** for same-org users, or **External** for cross-org testing. Under **Scopes**, add `https://www.googleapis.com/auth/bigquery.readonly`.
3. Go to **APIs & Services > Credentials**, click **Create Credentials > OAuth 2.0 Client ID**, and select **Web application** as the application type. Give it any name. You add the Aembit Callback URL under **Authorized redirect URIs** after you create the Credential Provider.
4. Grant each user the IAM roles they need on the GCP project or dataset: `roles/bigquery.dataViewer` to read datasets and tables, and `roles/bigquery.jobUser` to run queries. The OAuth scope alone isn’t sufficient.
5. Copy the **Client ID** and **Client Secret**, and store them for the Credential Provider configuration.

## Configure the Credential Provider

Create an MCP User-Based Access Token Credential Provider in Aembit.

1. Log into your Aembit Tenant.

2. Go to **Credential Providers** in the left sidebar and click **+ New**.

3. Configure the following fields:

   | Field               | Value                                               |
   | ------------------- | --------------------------------------------------- |
   | **Name**            | A user-friendly name                                |
   | **Credential Type** | MCP User-Based Access Token                         |
   | **MCP Server URL**  | `https://bigquery.googleapis.com/mcp`               |
   | **Client ID**       | The Client ID you copied earlier                    |
   | **Client Secret**   | The Client Secret you copied earlier                |
   | **Scopes**          | `https://www.googleapis.com/auth/bigquery.readonly` |
   | **PKCE Required**   | On                                                  |

   For **MCP Server URL**, click **Discover** to populate the Authorization URL and Token URL.

4. Click **Save**.

5. Copy the read-only **Callback URL** from the Credential Provider.

## Finish configuring the OAuth client

After you create the Credential Provider, copy its read-only **Callback URL** and return to the GCP OAuth 2.0 Client ID you created in **APIs & Services > Credentials**.

1. Open the OAuth 2.0 Client ID, and under **Authorized redirect URIs** click **Add URI**, paste the Aembit **Callback URL**, then click **Save**.
2. Confirm the Credential Provider scope matches the scope you added on the OAuth consent screen: `https://www.googleapis.com/auth/bigquery.readonly`

## Authorize the Credential Provider

1. Return to the Credential Provider in Aembit and click **Authorize**.

2. Choose your Google Account and approve access. The Credential Provider status changes to **Ready** when the flow completes.

## Create the Server Workload

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

This section creates the Gateway-to-Server Access Policy, which authorizes the MCP Identity Gateway to access Google BigQuery on behalf of authenticated users.

Create an Access Policy linking the MCP Identity Gateway (as the Client Workload), the Credential Provider you created, and the Server Workload for Google BigQuery.

> **The Gateway is the Client Workload**
>
> In this policy, the Client Workload is the MCP Identity Gateway itself—not the AI agent or individual users. The AI agent connects through the separate Client-to-Gateway policy, and the Credential Provider enforces per-user access.

For step-by-step instructions, including the Client Workload settings that identify the Gateway, see [Create the gateway-to-server Access Policy](../setup-mcp-gateway.md#create-the-gateway-to-server-access-policy).

## Verify

After a user authorizes access, the Aembit **AI Access Authorized** page lists the BigQuery MCP Server as **Ready**. The AI agent can then call BigQuery MCP tools (such as `execute_sql`) through the Gateway.
