---
type: how-to
title: "Atlassian MCP Server"
description: "Configure the Atlassian MCP Server to work with AI agents through the Aembit MCP Identity Gateway."
resource: https://docs.aembit.io/user-guide/deploy-install/mcp-identity-gateway/supported-servers/atlassian/
interface: mcp
tags: ["supported-server", "mcp-identity-gateway", "deploy-install"]
timestamp: 2026-09-22T15:44:57-07:00
---

# Atlassian MCP Server

Aembit supports the official [Atlassian MCP server](https://www.atlassian.com/platform/remote-mcp-server), which lets AI agents access Jira, Confluence, and Compass data through MCP tools.

This page describes how to configure Atlassian as an MCP server behind the Aembit MCP Identity Gateway.

This guide builds the Gateway-to-Server Policy—the second of the two Access Policies the MCP Identity Gateway requires. You create the first, the Client-to-Gateway Policy, during [Gateway setup](../../../access-policies/mcp-identity-gateway/setup-mcp-gateway.md).

> **Looking for direct Atlassian API access?**
>
> This page covers **MCP tool access through Aembit’s MCP Identity Gateway**. To broker credentials for Atlassian’s REST API as a traditional Server Workload, see the [Atlassian Server Workload guide](../../../access-policies/server-workloads/guides/atlassian.md) instead.

## Prerequisites

Before you begin, ensure you have the following:

* A paid Atlassian Cloud plan that includes Jira, Confluence, or Compass (no dedicated Rovo subscription required)
* A configured [Aembit MCP Identity Gateway](../../../access-policies/mcp-identity-gateway/setup-mcp-gateway.md)

## Requirements and considerations

Before you configure Atlassian, review these requirements and behaviors specific to the Atlassian MCP server.

* **Minimum MCP Gateway version.** Atlassian support requires MCP Gateway version 1.27.3975 or later.
* **Legacy endpoint deprecated.** Atlassian removes the legacy Server-Sent Events (SSE) endpoint `https://mcp.atlassian.com/v1/sse` after 30 June 2026; use `/v1/mcp` only.

## Choose an authentication approach

Aembit supports three authentication approaches for Atlassian. The tabs in the following sections cover the first two and stay in sync with the approach you choose. The third approach has its own section, [Set up enterprise-managed authentication](#set-up-enterprise-managed-authentication).

* **User-Based**: each user authenticates with their own Atlassian identity, and the Gateway injects that user’s token into MCP requests. Aembit stores and refreshes each user’s tokens individually.
* **Admin-Based**: an administrator completes the OAuth flow once during setup, and all users who access Atlassian through the Gateway share the resulting tokens.
* **[Enterprise-Managed](../../../access-policies/credential-providers/about-mcp-enterprise-managed-access-token.md)**: the corporate identity provider vouches for each user, and Aembit exchanges that assertion for the user’s Atlassian token with no consent prompt. Each user still receives their own token.

## Set up enterprise-managed authentication

Atlassian supports Enterprise-Managed Authorization for its MCP server, and Okta is the identity provider Atlassian names for it. Okta is also the only OIDC Identity Provider Aembit has verified for this flow. With this approach, the MCP Identity Gateway obtains each user’s Atlassian token through the corporate identity provider. No user sees an Atlassian consent prompt, and this Credential Provider type has no administrator authorization step.

1. In Atlassian Administration, select your organization if you have more than one, then select **Rovo** and **Rovo MCP server**, and open the **Authentication** tab.

2. Select **Edit URL** and enter your Okta issuer as the **trusted identity provider URL**. This is the same issuer your Aembit OIDC Identity Provider uses to sign users in. The [Atlassian enterprise-managed authentication documentation](https://support.atlassian.com/security-and-access-policies/docs/configuring-enterprise-managed-authentication/) covers this step in detail.

3. Complete the [`offline_access` scope](../../../access-policies/credential-providers/mcp-enterprise-managed-access-token-idp.md#add-the-offline_access-scope-in-aembit) and [token exchange](../../../access-policies/credential-providers/mcp-enterprise-managed-access-token-idp.md#enable-token-exchange-on-the-okta-application) sections of [Prepare the Identity Provider for enterprise-managed access](../../../access-policies/credential-providers/mcp-enterprise-managed-access-token-idp.md).

4. Complete [Allowlist your Aembit tenant](#allowlist-your-aembit-tenant) before you create the Credential Provider. **Discover** registers your Aembit tenant’s redirect URLs with Atlassian, so add the allowlist entry first.

5. Create the Credential Provider by following [Configure MCP Enterprise Managed Access Token](../../../access-policies/credential-providers/mcp-enterprise-managed-access-token.md). Enter `https://mcp.atlassian.com/v1/mcp/authv2` as the **MCP Server URL**, click **Discover**, and select the Okta Identity Provider as the **Corporate Identity Provider**.

6. Complete [Register the AI agent in Okta](../../../access-policies/credential-providers/mcp-enterprise-managed-access-token-idp.md#register-the-ai-agent-in-okta) with the Credential Provider’s **Client ID**, and then follow [Have users sign in again](../../../access-policies/credential-providers/mcp-enterprise-managed-access-token-idp.md#have-users-sign-in-again).

7. Continue with the following sections, [Create the Server Workload](#create-the-server-workload) and [Create an Access Policy](#create-an-access-policy), and attach this Credential Provider to the Access Policy.

> **Atlassian MCP V2 scopes**
>
> Atlassian has announced that MCP V2 ships with OAuth scopes that differ from V1. An organization that restricts Cross App Access scopes in Okta must add the V2 scopes after that release. For the current scope list, see the [Atlassian enterprise-managed authentication documentation](https://support.atlassian.com/security-and-access-policies/docs/configuring-enterprise-managed-authentication/).

## Allowlist your Aembit tenant

Before OAuth works, an Atlassian organization administrator must add your Aembit tenant domain to the Atlassian Rovo MCP server allowlist.

1. Go to `https://admin.atlassian.com/o/<your-org-id>/mcp-settings`. Ask your Atlassian organization administrator if you lack access.
2. Click **Add Domain**.
3. Add your Aembit tenant URL in the format `https://<your-tenant>/**`.

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
     | **MCP Server URL**  | `https://mcp.atlassian.com/v1/mcp`        |
     | **Client ID**       | Auto-populated—no pre-registration needed |
     | **Client Secret**   | Auto-populated by Discover                |
     | **PKCE Required**   | On                                        |

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
     | **Scopes**          | `read:all offline_access`                              |
     | **OAuth URL**       | `https://cf.mcp.atlassian.com`                         |
     | **PKCE Required**   | On                                                     |
     | **Lifetime**        | `1 year`                                               |

     For **OAuth URL**, click **URL Discovery** to populate the Authorization URL and Token URL.

  4. Click **Save**.

  5. Copy the read-only **Callback URL** from the Credential Provider.

## Register the OAuth client

* User-Based

  Atlassian’s MCP server supports OAuth Dynamic Client Registration, so you don’t need to register an app in an Atlassian console. When you click **Discover** on the Credential Provider, Aembit registers the OAuth client automatically. Atlassian grants access when the user completes the OAuth consent flow in the browser.

* Admin-Based

  Admin-Based Auth needs a Client ID. The Atlassian MCP server supports Dynamic Client Registration, so there’s no app to create in a console. Instead, an administrator obtains a Client ID with a one-time registration request to the Atlassian registration endpoint.

  1. Create the Credential Provider in Aembit and copy its read-only **Callback URL**.

  2. Run the following request, replacing `<credential-provider-callback-url>` with the Callback URL you copied and `<tenantId>` with your Aembit tenant:

     ```plaintext
     curl -X POST "https://cf.mcp.atlassian.com/v1/register" \
       -H "Content-Type: application/json" \
       -d '{
         "redirect_uris": ["<credential-provider-callback-url>"],
         "grant_types": ["authorization_code", "refresh_token"],
         "response_types": ["code"],
         "token_endpoint_auth_method": "none",
         "application_type": "native",
         "client_name": "<tenantId>.aembit.io"
       }'
     ```

  3. Copy the returned `client_id` into the Credential Provider configuration.

  4. When the Credential Provider asks for a **Client Secret**, enter any value—the registration is a public client.

## Authorize the Credential Provider

* User-Based

  There’s no administrator authorization step for this credential type. Each user completes their own Atlassian sign-in and consent the first time they access Atlassian through the Gateway, and Aembit stores and refreshes each user’s tokens individually.

* Admin-Based

  1. Return to the Credential Provider in Aembit and click **Authorize**.

  2. Complete the Atlassian sign-in as an administrator and approve access. The Credential Provider status changes to **Ready** when the flow completes.

## Create the Server Workload

1. Go to **Server Workloads** in the left sidebar and click **+ New**.

2. Configure the following fields:

   | Field                     | Value                |
   | ------------------------- | -------------------- |
   | **Name**                  | A user-friendly name |
   | **Host**                  | `mcp.atlassian.com`  |
   | **Application Protocol**  | MCP                  |
   | **Port**                  | 443 with TLS         |
   | **URL Path**              | `/v1/mcp`            |
   | **Authentication method** | HTTP Authentication  |
   | **Authentication scheme** | Bearer               |

   If you use enterprise-managed authentication, enter the `/v1/mcp/authv2` path as the **URL Path** instead.

3. Click **Save**.

## Create an Access Policy

This section creates the Gateway-to-Server Access Policy, which authorizes the MCP Identity Gateway to access Atlassian on behalf of authenticated users.

Create an Access Policy linking the MCP Identity Gateway (as the Client Workload), the Credential Provider you created, and the Server Workload for Atlassian.

> **The Gateway is the Client Workload**
>
> In this policy, the Client Workload is the MCP Identity Gateway itself—not the AI agent or individual users. The AI agent connects through the separate Client-to-Gateway policy, and the Credential Provider enforces per-user access.

For step-by-step instructions, including the Client Workload settings that identify the Gateway, see [Create the gateway-to-server Access Policy](../../../access-policies/mcp-identity-gateway/setup-mcp-gateway.md#create-the-gateway-to-server-access-policy).

## Verify

After authorization completes, the Aembit **AI Access Authorized** page lists the Atlassian MCP Server as **Ready**. The AI agent can then call Atlassian MCP tools through the Gateway.
