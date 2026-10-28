---
type: how-to
title: "GitLab MCP Server"
description: "Configure the GitLab MCP Server to work with AI agents through the Aembit MCP Identity Gateway."
resource: https://docs.aembit.io/user-guide/deploy-install/mcp-identity-gateway/supported-servers/gitlab/
interface: mcp
tags: ["supported-server", "mcp-identity-gateway", "deploy-install"]
timestamp: 2026-10-28T08:39:08-07:00
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

Register a GitLab OAuth application and use its application ID and secret in the MCP User-Based Access Token Credential Provider.

1. Log in to your Aembit Tenant.

2. Go to **Credential Providers** and click **+ New**.

3. Enter a **Name** and select **MCP User-Based Access Token** as the **Credential Type**.

4. Set **MCP Server URL** to `https://gitlab.com/api/v4/mcp` and click **Discover** to populate the OAuth URLs.

5. Copy the read-only **Callback URL**. Keep this Credential Provider form open while you register the application in GitLab.

6. In GitLab, open your top-level group and go to **Settings > Applications**.

7. Enter a **Name** and set **Redirect URI** to the exact **Callback URL** you copied from Aembit.

8. Select the `mcp` scope and click **Save application**.

9. Copy the **Application ID** and **Secret** from GitLab. Store the secret securely. GitLab shows the secret only immediately after application creation. Copy it before you leave the page.

10. Return to the same Aembit Credential Provider form and enter the following values:

    | Field             | Value                         |
    | ----------------- | ----------------------------- |
    | **Client Id**     | The GitLab **Application ID** |
    | **Client Secret** | The GitLab **Secret**         |
    | **Scopes**        | `mcp`                         |
    | **PKCE Required** | On                            |

11. Click **Save**.

12. Compare the saved Credential Provider’s **Callback URL** with the GitLab application’s **Redirect URI**. If they differ, update the application’s **Redirect URI** to match the saved **Callback URL**.

When you create the Access Policy that grants MCP Identity Gateway access to GitLab, use the **Select Existing** tab. Select the Credential Provider you saved here and click **Use Selected**. Don’t create another Credential Provider: its **Callback URL** won’t match the GitLab application’s **Redirect URI**. Don’t run **Discover** again after entering the application credentials, because it overwrites them.

For details, see GitLab’s [Create a group-owned application](https://docs.gitlab.com/integration/oauth_provider/#create-a-group-owned-application).

## User authorization

Each user authorizes access to GitLab when they first connect through MCP Identity Gateway. The MCP User-Based Access Token Credential Provider has no administrator **Authorize** step.

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

On the **Select Existing** tab, select the Credential Provider you saved during GitLab setup and click **Use Selected**. The linked setup procedure describes creating a new Credential Provider. Reuse yours so its **Callback URL** matches GitLab’s **Redirect URI**.

This section creates the Gateway-to-Server Access Policy, which authorizes the MCP Identity Gateway to access GitLab on behalf of authenticated users.

Create an Access Policy linking the MCP Identity Gateway (as the Client Workload), the Credential Provider you created, and the Server Workload for GitLab.

> **The Gateway is the Client Workload**
>
> In this policy, the Client Workload is the MCP Identity Gateway itself—not the AI agent or individual users. The AI agent connects through the separate Client-to-Gateway policy, and the Credential Provider enforces per-user access.

For step-by-step instructions, including the Client Workload settings that identify the Gateway, see [Create the gateway-to-server Access Policy](../../../access-policies/mcp-identity-gateway/setup-mcp-gateway.md#create-the-gateway-to-server-access-policy).

## Verify

After a user authorizes access, the Aembit **AI Access Authorized** page lists the GitLab MCP Server as **Ready**. The AI agent can then call GitLab MCP tools through the Gateway.
