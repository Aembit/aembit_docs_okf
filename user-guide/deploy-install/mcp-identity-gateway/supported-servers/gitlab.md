---
type: how-to
title: "GitLab MCP Server"
description: "Configure the GitLab MCP Server to work with AI agents through the Aembit MCP Identity Gateway."
resource: https://docs.aembit.io/user-guide/deploy-install/mcp-identity-gateway/supported-servers/gitlab/
interface: mcp
tags: ["supported-server", "mcp-identity-gateway", "deploy-install"]
timestamp: 2026-09-22T15:09:30-07:00
---

# GitLab MCP Server

Aembit supports the official [GitLab MCP server](https://docs.gitlab.com/user/model_context_protocol/mcp_server/), which lets AI agents access GitLab project data through MCP tools.

This page describes how to configure GitLab as an MCP server behind the Aembit MCP Identity Gateway. Each user authenticates with their own GitLab identity, and the Gateway injects their token into MCP requests.

This guide builds the Gateway-to-Server Policy—the second of the two Access Policies the MCP Identity Gateway requires. You create the first, the Client-to-Gateway Policy, during [Gateway setup](../../../access-policies/mcp-identity-gateway/setup-mcp-gateway.md).

> **Looking for direct GitLab API access?**
>
> This page covers **MCP tool access through Aembit’s MCP Identity Gateway**. To broker credentials for GitLab’s REST API as a traditional Server Workload, see the [GitLab Server Workload guide](../../../access-policies/server-workloads/guides/gitlab-rest.md) instead.

## Prerequisites

Before you begin, ensure you have the following:

* The Owner role for a GitLab.com top-level group, which is what enabling the MCP server requires
* A configured [Aembit MCP Identity Gateway](../../../access-policies/mcp-identity-gateway/setup-mcp-gateway.md)

## Requirements and considerations

Before you configure GitLab, review these requirements and behaviors specific to GitLab’s MCP server.

* **Use User-Based Auth.** Aembit supports User-Based Auth for GitLab.
* **GitLab.com only.** Aembit verified this configuration against GitLab.com. GitLab also offers the MCP server on GitLab Self-Managed and GitLab Dedicated, which Aembit hasn’t tested.
* **The MCP server is a GitLab Beta feature.** GitLab can change the server’s behavior between releases.
* **Request the `mcp` scope alone.** GitLab rejects the authorization request when the scope list includes `offline_access`.

## Enable the MCP server in GitLab

GitLab turns the MCP server off by default, so a group Owner enables it on the top-level group before users can authenticate.

1. In GitLab, open the top-level group and go to **Settings > General**.
2. Expand **Permissions and group features**.
3. In the **MCP client access** section, select **Allow connection to GitLab**.
4. Click **Save changes**.

## Configure the Credential Provider

Create an MCP User-Based Access Token Credential Provider in Aembit.

1. Log into your Aembit Tenant.

2. Go to **Credential Providers** in the left sidebar and click **+ New**.

3. Configure the following fields:

   | Field               | Value                                     |
   | ------------------- | ----------------------------------------- |
   | **Name**            | A user-friendly name                      |
   | **Credential Type** | MCP User-Based Access Token               |
   | **MCP Server URL**  | `https://gitlab.com/api/v4/mcp`           |
   | **Client ID**       | Auto-populated—no pre-registration needed |
   | **Client Secret**   | Auto-populated by Discover                |
   | **Scopes**          | `mcp`                                     |
   | **PKCE Required**   | On                                        |

   For **MCP Server URL**, click **Discover** to populate the Authorization URL and Token URL.

4. Click **Save**.

5. Copy the read-only **Callback URL** from the Credential Provider.

## Finish configuring the OAuth client

GitLab supports OAuth Dynamic Client Registration, so you don’t need to register an application in GitLab.

When you create the MCP User-Based Access Token Credential Provider and click **Discover**, GitLab fills in the client registration automatically—Client ID, Client Secret, and URLs. GitLab grants access when each user completes the OAuth consent flow in the browser.

## Authorize the Credential Provider

1. Return to the Credential Provider in Aembit and click **Authorize**.

2. Complete the GitLab sign-in and approve access. The Credential Provider status changes to **Ready** when the flow completes.

## Create the Server Workload

1. Go to **Server Workloads** in the left sidebar and click **+ New**.

2. Configure the following fields:

   | Field                     | Value                |
   | ------------------------- | -------------------- |
   | **Name**                  | A user-friendly name |
   | **Host**                  | `gitlab.com`         |
   | **Application Protocol**  | MCP                  |
   | **Port**                  | 443 with TLS         |
   | **URL Path**              | `/api/v4/mcp`        |
   | **Authentication method** | HTTP Authentication  |
   | **Authentication scheme** | Bearer               |

3. Click **Save**.

## Create an Access Policy

This section creates the Gateway-to-Server Access Policy, which authorizes the MCP Identity Gateway to access GitLab on behalf of authenticated users.

Create an Access Policy linking the MCP Identity Gateway (as the Client Workload), the Credential Provider you created, and the Server Workload for GitLab.

> **The Gateway is the Client Workload**
>
> In this policy, the Client Workload is the MCP Identity Gateway itself—not the AI agent or individual users. The AI agent connects through the separate Client-to-Gateway policy, and the Credential Provider enforces per-user access.

For step-by-step instructions, including the Client Workload settings that identify the Gateway, see [Create the gateway-to-server Access Policy](../../../access-policies/mcp-identity-gateway/setup-mcp-gateway.md#create-the-gateway-to-server-access-policy).

## Verify

After a user authorizes access, the Aembit **AI Access Authorized** page lists the GitLab MCP Server as **Ready**. The AI agent can then call GitLab MCP tools through the Gateway.
