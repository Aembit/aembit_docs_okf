---
type: how-to
title: "Set up the MCP Identity Gateway"
description: "Configure your Aembit Tenant for the managed MCP Identity Gateway."
resource: https://docs.aembit.io/user-guide/access-policies/mcp-identity-gateway/setup-mcp-gateway/
interface: mcp
tags: ["mcp-identity-gateway", "access-policy"]
timestamp: 2026-09-22T15:44:57-07:00
---

# Set up the MCP Identity Gateway

This guide explains how to configure your Aembit Tenant for the Model Context Protocol (MCP) Identity Gateway, which Aembit operates as a managed service.

> **Running the Gateway yourself?**
>
> This guide covers the recommended managed service, where Aembit hosts and operates the Gateway for you. If you must run the Gateway in your own infrastructure instead, see [Self-host the MCP Identity Gateway](../../deploy-install/mcp-identity-gateway/self-host-mcp-gateway.md) for the host-side deployment steps. Then return here for the Aembit Cloud configuration.

## Prerequisites

Before you begin, ensure you have:

* An Aembit Tenant with admin access
* A provisioned MCP Identity Gateway endpoint for your Tenant (`https://<tenantId>.mcpgateway.aembit.io`). If you don’t have one yet, contact your Aembit representative.
* An identity provider (Okta, Google, Microsoft Entra ID, or similar) for user authentication
* The OAuth redirect URLs of the MCP clients (AI agents) you plan to connect
* The hostname and OAuth configuration for each MCP server you plan to proxy

> **MCP resource support**
>
> Aembit’s MCP Identity Gateway automatically proxies MCP resource requests using the same Access Policies and credential management as tool requests. You don’t need to configure additional Access Policies or change existing ones.
>
> For details, see [MCP resource support](../../deploy-install/mcp-identity-gateway/concepts-mcp-gateway.md#mcp-resource-support).

## Request your MCP Identity Gateway endpoint

Aembit operates the MCP Identity Gateway as a managed service. Each Aembit Tenant has a per-Tenant Gateway endpoint at `https://<tenantId>.mcpgateway.aembit.io` (replace `<tenantId>` with your Aembit Tenant ID, visible in your Aembit Tenant URL, for example, `abc123` in `https://abc123.aembit.io`).

To get started, contact your Aembit representative to provision a Gateway endpoint for your Tenant. Aembit handles provisioning, TLS termination, certificate management, and runtime operations.

Once provisioned, note the Gateway hostname. You’ll reference it later in this guide when configuring the Server Workload and Credential Provider for the client-to-Gateway Access Policy.

> **Provisioning model**
>
> Aembit provisions MCP Identity Gateway endpoints by request. Self-service provisioning isn’t yet available.

## Configure an Identity Provider

Connect Aembit to your enterprise Identity Provider (IdP) to authenticate the humans behind MCP clients.

Unlike typical Aembit workload-to-workload scenarios, MCP requests involve two identities:

* **MCP client identity** (workload) - The AI agent or application making the request, identified by its redirect URL.
* **User identity** (human) - The person using the AI agent, identified by claims from your IdP (such as email address).

The MCP Identity Gateway needs both: it validates *which* MCP client is connecting and *who* is using it. The Credential Provider then stores tokens per-user, tying each user’s MCP server access to their own authorization.

In this model, you configure identity once at the tenant level. Many users can share the same set of policies, while the per-user credentials the Credential Provider manages isolate their individual access.

In your Aembit Tenant, go to **Administration -> Identity Providers** and configure your IdP using [OIDC](../../administration/identity-providers/create-idp-oidc.md) or [SAML](../../administration/identity-providers/create-idp-saml.md). The MCP Enterprise Managed Access Token Credential Provider requires an OIDC Identity Provider with the `offline_access` scope; see [Prepare the Identity Provider for enterprise-managed access](../credential-providers/mcp-enterprise-managed-access-token-idp.md).

> **Automating configuration**
>
> Automate Workload, Trust Provider, Credential Provider, and Access Policy creation using the [Aembit Terraform provider](../advanced-options/terraform/terraform-configuration.md) or the [Aembit API](../../../dev-guide/api/overview.md).

## Create the client-to-gateway Access Policy

The MCP Identity Gateway requires **two Access Policies** to function: one for the client-to-Gateway connection and one for the Gateway-to-server connection. You must create both policies for the Gateway to work.

![Two Access Policies required for the MCP Identity Gateway: client-to-Gateway and Gateway-to-server](https://docs.aembit.io/d2/docs/user-guide/access-policies/mcp-identity-gateway/setup-mcp-gateway-0.svg)

This section covers creating the first policy, which governs which MCP clients and users can connect to the MCP Identity Gateway. For step-by-step instructions on using the Access Policy Builder interface, see [Create an Access Policy](../create-access-policy.md).

### Name the Access Policy

1. While still in your Aembit Tenant, go to **Access Policies** and click **+ New**.
2. In the **Name** field, enter a name (for example, `MCP Client to Gateway`).

### Client Workload

Create a Client Workload that represents the MCP client connecting to the Gateway.

1. In the **Client Workload** card in the right panel, click **+ Configure**.

2. Select the **Add New** tab.

3. Configure the Client Workload with the following settings:

   | Field                 | Value                                                                                        |
   | --------------------- | -------------------------------------------------------------------------------------------- |
   | Name                  | A descriptive name (for example, `Claude MCP Client`)                                        |
   | Client Identification | Select **Redirect URL**                                                                      |
   | Value                 | The MCP client’s OAuth callback URL (for example, `https://claude.ai/api/mcp/auth_callback`) |

4. Click **Save**.

The Redirect URL identifies which MCP client application is requesting access. Each MCP client (Claude, Gemini, etc.) has its own callback URL. See [Client Workloads](../client-workloads/overview.md) for additional configuration options.

### Server Workload

Create a Server Workload that represents the MCP Identity Gateway endpoint.

1. In the **Server Workload** card in the right panel, click **+ Configure**.

2. Select the **Add New** tab.

3. Configure the Server Workload with the following settings:

   | Field                | Value                                                                                   |
   | -------------------- | --------------------------------------------------------------------------------------- |
   | Name                 | A descriptive name (for example, `MCP Identity Gateway`)                                |
   | Host                 | The Gateway’s hostname for your Tenant (for example, `<tenantId>.mcpgateway.aembit.io`) |
   | Application Protocol | Select **MCP**                                                                          |
   | Port                 | **443**                                                                                 |
   | TLS                  | Enabled                                                                                 |
   | URL Path             | `/mcp`                                                                                  |

4. Click **Save**.

> **Set URL Path to `/mcp`**
>
> The **URL Path** field is an Aembit policy-matching identifier. The Gateway always exposes MCP traffic at `/mcp`, so MCP clients connect to `https://<gateway-host>/mcp`. Setting **URL Path** to `/mcp` matches this path exactly, which is the most precise policy configuration.

See [Server Workloads](../server-workloads/overview.md) for additional configuration options.

### Trust Provider

Create a Trust Provider that validates user tokens from your enterprise IdP.

1. In the **Trust Provider** card in the right panel, click **+ Configure**.

2. Select the **Add New** tab.

3. Configure the Trust Provider with the following settings:

   | Field              | Value                                                                    |
   | ------------------ | ------------------------------------------------------------------------ |
   | Name               | A descriptive name (for example, `Okta User Tokens`)                     |
   | Trust Provider     | Select **OIDC ID Token**                                                 |
   | OIDC Discovery URL | Your IdP’s discovery endpoint (for example, `https://your-org.okta.com`) |
   | Match Rules        | Configure issuer validation                                              |

4. Configure **Match Rules** to validate identity token claims:

   * **Issuer (`iss`)** - Your IdP’s URL (for example, `https://your-org.okta.com`). This must match the OIDC Discovery URL.
   * **Audience (`aud`)** - (Optional) The intended recipient identifier.
   * **Subject (`sub`)** - (Optional) User identifier pattern to match.

5. Click **Save**.

Find your IdP’s OIDC discovery URL in your identity provider’s administrator console. Enter only the base URL (for example, `https://your-org.okta.com`). Aembit automatically appends `/.well-known/openid-configuration` to discover the provider’s configuration. See [Trust Providers](../trust-providers/overview.md) for additional configuration options.

### Credential Provider

Create a Credential Provider that issues tokens for MCP clients to authenticate to the Gateway.

1. In the **Credential Provider** card in the right panel, click **+ Configure**.

2. Select the **Add New** tab.

3. Configure the Credential Provider with the following settings:

   | Field                  | Value                                                                                                                        |
   | ---------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
   | Name                   | A descriptive name (for example, `Gateway Access Token`)                                                                     |
   | Credential Type        | Select **OIDC ID Token**                                                                                                     |
   | Subject                | Select **Dynamic** from the dropdown, then enter `${oidc.identityToken.decode.payload.<user_claim>}` (see the following tip) |
   | Audience               | The Gateway URL **with a trailing slash** (for example, `https://mcp-gateway.example.com/`)                                  |
   | Signing Algorithm Type | **RS256** (recommended) or ES256                                                                                             |

4. Click **Save**.

> **UI Terminology**
>
> The Aembit Cloud UI labels this credential type as ‘OIDC ID Token’, but what’s generated is an OAuth 2.0 access token in JWT format. This documentation uses ‘access token’ in conceptual explanations while preserving the exact UI field values in step-by-step instructions.

> **Choosing a user identifier claim**
>
> Replace `<user_claim>` with a claim from your IdP that uniquely identifies users (for example, `email`, `sub`, or `preferred_username`). The exact claim name depends on your Identity Provider. Check your IdP’s token documentation to find available claims.
>
> If your Identity Provider uses SAML, set **Subject** to `${saml.response.subject.nameId}` instead. See [SAML assertion claims](../credential-providers/advanced-options/dynamic-claims.md#saml-assertion-claims).
>
> Unlike typical workload-to-workload scenarios, MCP traffic involves a human user. This dynamic claim identifies *who* is using the AI agent, enabling per-user access control. See [User identity in MCP traffic](../../deploy-install/mcp-identity-gateway/concepts-mcp-gateway.md#user-identity-in-mcp-traffic) for details, or [Dynamic Claims](../credential-providers/advanced-options/dynamic-claims.md) for the full syntax reference.

The token contains the authenticated user’s identity (for example, an email claim) and targets **only** the MCP Identity Gateway. See [Credential Providers](../credential-providers/overview.md) for additional configuration options.

### Save the Access Policy

1. In the **Access Policy** status card on the left, verify each required component shows a green **Configured** status.
2. In the header, click **Save Policy & Activate**.

## Create the gateway-to-server Access Policy

The second policy governs the MCP Identity Gateway’s access to each MCP server. The Credential Provider enforces per-user access by storing OAuth tokens for each user.

> **Per-user access control**
>
> In this policy, the **Client Workload** is the MCP Identity Gateway itself, not individual users. The Credential Provider enforces per-user access: with MCP User-Based Access Token, each user completes an OAuth authorization flow once per MCP server, and Aembit stores their tokens individually. With MCP Enterprise Managed Access Token, Aembit exchanges each user’s SSO identity for the MCP server token, and no user sees a consent prompt. Optional Access Conditions can add Time or GeoIP restrictions.

### Name the Access Policy

1. While still in your Aembit Tenant, go to **Access Policies** and click **+ New**.
2. In the **Name** field, enter a name (for example, `Gateway to Finance MCP Server`).

### Client Workload

Create a Client Workload that represents the MCP Identity Gateway as a client.

1. In the **Client Workload** card in the right panel, click **+ Configure**.

2. Select the **Add New** tab.

3. Configure the Client Workload with the following settings:

   | Field                 | Value                                                                                       |
   | --------------------- | ------------------------------------------------------------------------------------------- |
   | Name                  | A descriptive name (for example, `MCP Identity Gateway`)                                    |
   | Client Identification | Select **OIDC ID Token Audience**                                                           |
   | Value                 | The Gateway URL **with a trailing slash** (for example, `https://mcp-gateway.example.com/`) |

   The Audience value must include the trailing slash and match the audience that the left-side policy’s Credential Provider issues, so Aembit recognizes the Gateway as the authenticated caller.

4. Click **Save**.

The Gateway acts as the client when accessing MCP servers. See [Client Workloads](../client-workloads/overview.md) for additional configuration options.

### Server Workload

Create a Server Workload for each downstream MCP server.

1. In the **Server Workload** card in the right panel, click **+ Configure**.

2. Select the **Add New** tab.

3. Configure the Server Workload with the following settings:

   | Field                | Value                                                      |
   | -------------------- | ---------------------------------------------------------- |
   | Name                 | A descriptive name (for example, `Finance MCP Server`)     |
   | Host                 | The MCP server’s hostname (for example, `mcp.example.com`) |
   | Application Protocol | Select **MCP**                                             |
   | Port                 | Typically **443** with TLS enabled                         |
   | URL Path             | The MCP endpoint path (for example, `/integrations/mcp`)   |

4. Click **Save**.

Find the hostname, path, and port in your MCP server vendor’s documentation. See [Server Workloads](../server-workloads/overview.md) for additional configuration options.

### Trust Provider

Create a Trust Provider that validates Aembit-issued tokens.

1. In the **Trust Provider** card in the right panel, click **+ Configure**.

2. Select the **Add New** tab.

3. Configure the Trust Provider with the following settings:

   | Field              | Value                                                                       |
   | ------------------ | --------------------------------------------------------------------------- |
   | Name               | A descriptive name (for example, `Aembit MCP Tokens`)                       |
   | Trust Provider     | Select **OIDC ID Token**                                                    |
   | OIDC Discovery URL | Your Aembit Tenant’s base URL (for example, `https://<tenantId>.aembit.io`) |

4. Configure **Match Rules** to validate Aembit-issued tokens:

   * **Audience (`aud`)** - The Gateway identifier (for example, `gateway`).
   * **Issuer (`iss`)** - (Optional) Your Aembit Tenant URL (for example, `https://<tenantId>.aembit.io`). If you configure this, this must match the OIDC Discovery URL.

5. Click **Save**.

See [Trust Providers](../trust-providers/overview.md) for additional configuration options, including how to locate your tenant’s OIDC discovery URL.

### Credential Provider

Create a Credential Provider that retrieves credentials for the MCP server. The MCP Identity Gateway supports three credential provider types for this policy, depending on how the MCP server issues credentials.

#### Choosing a credential type

|                        | MCP User-Based Access Token                                                                     | MCP Enterprise Managed Access Token                                                                                                                               | OAuth 2.0 Authorization Code                                                        |
| ---------------------- | ----------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| **Token storage**      | Per user. Aembit stores and refreshes each user’s tokens individually.                          | Per user. Aembit stores each user’s SSO identity at sign-in and caches the exchanged MCP server token for up to five minutes.                                     | Per credential provider. One set of tokens, shared across all users.                |
| **Authorization flow** | Each user completes their own OAuth consent flow on first access through the Gateway.           | None for the user. The corporate identity provider authorizes the exchange, and no consent prompt appears.                                                        | An administrator clicks **Authorize** in the Aembit UI during setup.                |
| **Best for**           | SaaS MCP servers that issue credentials scoped to individual users (most common).               | MCP servers that support Enterprise-Managed Authorization, with an OIDC Identity Provider that supports token exchange. Okta is the only one Aembit has verified. | MCP servers that accept a shared service account or administrator-authorized token. |
| **Discovery**          | **MCP Server URL** field with **Discover** auto-populates OAuth endpoints from server metadata. | **MCP Server URL** field with **Discover** fills the OAuth endpoints and the Client ID from server metadata.                                                      | **OAuth URL** field with **URL Discovery** auto-populates endpoints.                |

Most SaaS MCP servers require per-user credentials. Use **MCP User-Based Access Token** unless the MCP server explicitly accepts shared credentials. When the MCP server and your OIDC Identity Provider both support Enterprise-Managed Authorization, use **MCP Enterprise Managed Access Token** instead, so users never see a consent prompt.

> **Where to find OAuth client settings**
>
> The Client ID, Client Secret, Scopes, and OAuth endpoint URLs come from your MCP server vendor. To find these values:
>
> * **Check the vendor’s developer documentation** for OAuth or API integration guides.
> * **Look in the vendor’s administrator console** for an app registration or API credentials section.
> * **Use dynamic client registration** if the vendor supports it. Some MCP servers let you register a client by sending a POST request to their registration endpoint, which returns a Client ID automatically.
> * **Use Discover/URL Discovery** after entering the MCP Server URL or OAuth URL. Aembit can auto-populate endpoint URLs and scopes from the server’s published OAuth metadata.

* MCP User-Based Access Token

  1. In the **Credential Provider** card in the right panel, click **+ Configure**.

  2. Select the **Add New** tab.

  3. Configure the Credential Provider with the following settings:

     | Field           | Value                                                                  |
     | --------------- | ---------------------------------------------------------------------- |
     | Name            | A descriptive name (for example, `Finance MCP User Token`)             |
     | Credential Type | Select **MCP User-Based Access Token**                                 |
     | MCP Server URL  | The MCP server’s base URL (for example, `https://mcp.example.com/mcp`) |

  4. Click **Discover** to auto-populate the Authorization URL, Token URL, and Scopes from the MCP server’s metadata.

     If the MCP server doesn’t support discovery, enter the Authorization URL and Token URL manually using values from the MCP server vendor’s documentation.

  5. Enter the remaining OAuth client settings:

     | Field         | Value                                                                |
     | ------------- | -------------------------------------------------------------------- |
     | Client ID     | Your OAuth client ID for the MCP server                              |
     | Client Secret | The OAuth client secret, if required                                 |
     | Scopes        | Required scopes (may be auto-populated by Discover)                  |
     | PKCE Required | Enable if the MCP server requires Proof Key for Code Exchange (PKCE) |

  6. Click **Save**.

  > **Per-user authorization**
  >
  > Unlike the OAuth 2.0 Authorization Code Credential Provider, this type doesn’t require an administrator to click **Authorize** during setup. Instead, each user completes their own OAuth consent flow the first time they access the MCP server through the Gateway. Aembit stores and refreshes each user’s tokens individually.

* MCP Enterprise Managed Access Token

  Before you start, complete the [`offline_access` scope](../credential-providers/mcp-enterprise-managed-access-token-idp.md#add-the-offline_access-scope-in-aembit) and [token exchange](../credential-providers/mcp-enterprise-managed-access-token-idp.md#enable-token-exchange-on-the-okta-application) sections of [Prepare the Identity Provider for enterprise-managed access](../credential-providers/mcp-enterprise-managed-access-token-idp.md).

  1. In the **Credential Provider** card in the right panel, click **+ Configure**.

  2. Select the **Add New** tab.

  3. Configure the Credential Provider with the following settings:

     | Field           | Value                                                                                                   |
     | --------------- | ------------------------------------------------------------------------------------------------------- |
     | Name            | A descriptive name (for example, `Finance MCP Enterprise Token`)                                        |
     | Credential Type | Select **MCP Enterprise Managed Access Token**                                                          |
     | OIDC Issuer URL | The issuer identifier of the MCP server’s authorization server (for example, `https://mcp.example.com`) |
     | MCP Server URL  | The MCP server’s base URL (for example, `https://mcp.example.com/mcp`)                                  |

  4. Click **Discover** to fill the Client ID, Scopes, Authorization URL, Token URL, and Introspection URL from the MCP server’s metadata.

     If the MCP server doesn’t support discovery, enter them manually using values from the MCP server vendor’s documentation.

  5. Enter the remaining fields:

     | Field                          | Value                                                                                                                                                           |
     | ------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
     | Client ID                      | The OAuth client identifier the MCP server’s authorization server issued for Aembit. **Discover** fills it when the server supports dynamic client registration |
     | Scopes                         | Required scopes, which **Discover** may fill                                                                                                                    |
     | Introspection URL              | (Optional) The MCP server’s token introspection endpoint                                                                                                        |
     | Identity Assertion Issuer Mode | Leave at the default, **Corporate Identity Provider**                                                                                                           |
     | Corporate Identity Provider    | Select the OIDC Identity Provider your users sign in to Aembit through                                                                                          |

  6. If your Corporate Identity Provider is Okta, remove the `email` and `offline_access` scopes from this Credential Provider’s **Scopes** field.

     **Discover** adds both scopes, and the token exchange with Okta fails while they’re present.

  7. Click **Save**.

  8. Complete [Register the AI agent in Okta](../credential-providers/mcp-enterprise-managed-access-token-idp.md#register-the-ai-agent-in-okta) with the Credential Provider’s **Client ID**.

  > **Per-user authorization without a consent prompt**
  >
  > Unlike MCP User-Based Access Token, this type shows no consent prompt to the user. Aembit exchanges the user’s SSO identity for the MCP server token when the user first sends a request through the Gateway. Users who signed in to Aembit before you added the `offline_access` scope must sign out and sign in again; see [Configure MCP Enterprise Managed Access Token](../credential-providers/mcp-enterprise-managed-access-token.md) for the full field reference.

* OAuth 2.0 Authorization Code

  1. In the **Credential Provider** card in the right panel, click **+ Configure**.

  2. Select the **Add New** tab.

  3. Configure the Credential Provider with the following settings:

     | Field           | Value                                                          |
     | --------------- | -------------------------------------------------------------- |
     | Name            | A descriptive name (for example, `Finance Shared OAuth Token`) |
     | Credential Type | Select **OAuth 2.0 Authorization Code**                        |

  4. Enter the OAuth client settings:

     | Field             | Value                                                                                                                                                             |
     | ----------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
     | Client ID         | Your OAuth client ID for the MCP server                                                                                                                           |
     | Client Secret     | The OAuth client secret, if required                                                                                                                              |
     | Scopes            | Required scopes (for example, `read:all offline_access`)                                                                                                          |
     | OAuth URL         | The base URL of the OAuth endpoints (for example, `https://mcp.example.com/oauth`). Click **URL Discovery** to auto-populate the Authorization URL and Token URL. |
     | Authorization URL | The endpoint where the user authenticates and grants consent (auto-populated by URL Discovery if available)                                                       |
     | Token URL         | The endpoint that exchanges authorization codes for access tokens (auto-populated by URL Discovery if available)                                                  |
     | PKCE Required     | Enable if the MCP server requires Proof Key for Code Exchange (PKCE)                                                                                              |
     | Lifetime          | The expected lifetime of the authorization (for example, `1 year`)                                                                                                |

  5. Click **Save**.

  6. After saving, click **Authorize** to complete the OAuth flow. An administrator must authenticate with the MCP server and grant access. All users who access this MCP server through the Gateway share the resulting tokens.

  > **Shared authorization**
  >
  > With this credential type, an administrator completes the OAuth flow once during setup. All users who access this MCP server through the Gateway share the same credentials. If the authorization expires, an administrator must re-authorize in the Aembit UI.

> **OAuth endpoint sources**
>
> OAuth endpoint URLs vary by MCP server vendor. Consult your MCP server’s documentation for these values, or use the discovery button to auto-populate them from the server’s metadata.

For more information, see:

* [MCP User-Based Access Tokens](../credential-providers/about-mcp-user-based-access-token.md) for guidance on when each type is appropriate
* [Configure MCP User-Based Access Token](../credential-providers/mcp-user-based-access-token.md) for the full MCP User-Based configuration reference
* [MCP Enterprise Managed Access Tokens](../credential-providers/about-mcp-enterprise-managed-access-token.md) for when the enterprise-managed type fits
* [Configure MCP Enterprise Managed Access Token](../credential-providers/mcp-enterprise-managed-access-token.md) for the full MCP Enterprise Managed configuration reference
* [OAuth 2.0 Authorization Code](../credential-providers/oauth-authorization-code.md) for the full OAuth 2.0 configuration reference
* [Credential Providers](../credential-providers/overview.md) for all available Credential Provider types

### Access Conditions (optional)

Use Access Conditions to add contextual restrictions to the policy. The MCP Identity Gateway supports time-based and GeoIP-based conditions for MCP traffic.

To add Access Conditions, in the **Access Conditions** card in the right panel, click **+ Configure** and configure as needed. See [Access Conditions](../access-conditions/overview.md) for available condition types and configuration options.

> **Per-user access**
>
> Access Conditions are optional. Without them, any authenticated user routed through the Gateway can access the MCP server.
>
> Each user has their own OAuth tokens managed by the Credential Provider. When the Gateway acts on behalf of a user, it retrieves that user’s specific credentials.

### Content Security (optional)

Adding a Content Security Provider to this policy makes the Gateway apply that provider’s decision to each MCP tool message the policy matches.

To add Content Security, in the **Content Security** card in the right panel, click **+ Configure**. Then select an existing Content Security Provider or create a new one. See [Add CrowdStrike AIDR to a policy](../content-security/crowdstrike-aidr/add-to-policy.md) for the full configuration steps.

For how inspection works in the Gateway request path, see [Content Security in the MCP Identity Gateway](content-security-mcp-gateway.md).

### Save the Access Policy

1. In the **Access Policy** status card on the left, verify each required component shows a green **Configured** status.
2. In the header, click **Save Policy & Activate**.

When the MCP Identity Gateway connects to the Aembit control plane, it automatically discovers which MCP servers are available based on the Access Policies that apply to it. This policy-driven discovery means you can add or remove MCP servers by updating your Aembit configuration without modifying the Gateway itself.

### User authorization flow

For MCP servers that require user authorization (for example, via OAuth 2.0 flows), the first access triggers a consent flow:

1. When a user first attempts to access a given MCP server via the MCP Identity Gateway, Aembit detects that no credentials exist for that user and MCP server.
2. Aembit starts an OAuth 2.0 authorization flow and directs the user to authenticate with your IdP and approve the MCP integration (for example, by opening a browser flow).
3. After the user completes the flow, Aembit obtains credentials from the MCP server’s authorization infrastructure and associates those credentials with that user and MCP server.

Subsequent access for that user and server proceeds without repeated consent, subject to token expiration and revocation policies.

This consent flow applies to MCP User-Based Access Token. With [MCP Enterprise Managed Access Token](../credential-providers/about-mcp-enterprise-managed-access-token.md) no consent flow runs, because Aembit exchanges the user’s SSO identity for the token.

## Verify the connection

> **Activate both policies**
>
> Both the client-to-Gateway and Gateway-to-server Access Policies must be active before testing. If you didn’t activate them during creation, go to **Access Policies** and enable the **Active** toggle for each policy.

Once configuration is complete, verify the end-to-end flow:

1. Verify the Gateway endpoint is reachable from a client host:

   ```shell
   curl -I "https://<tenantId>.mcpgateway.aembit.io/"
   ```

   Expected: TLS handshake succeeds (you’ll get an HTTP response, possibly an error since this isn’t a valid MCP request). If this fails, check DNS resolution and confirm with your Aembit representative that the Gateway endpoint is active.

2. Confirm the user can authenticate via the IdP and that the MCP Identity Gateway receives a valid token. Use your MCP client to initiate a connection. The IdP / consent flow should appear in the user’s browser.

   If this fails, check the Trust Provider configuration in Aembit and verify the IdP endpoints are correct.

3. Verify the MCP Identity Gateway can evaluate Access Policies and that permitted requests succeed. After authentication, the MCP client should be able to list tools or resources.

   If this fails, check the Access Policy configuration in Aembit. Ensure both Client-to-Gateway and Gateway-to-Server policies are active.

4. Review MCP activity in Aembit Cloud:

   * **Workload events** - Check `mcp.request` and `mcp.response` events in Aembit Cloud for authentication, policy evaluation, and MCP request handling. See [Audit and report on Workload activity](../../audit-report/overview.md).
   * **Log Streams** - Forward workload events to your SIEM or observability tooling via [Log Streams](../../administration/log-streams/overview.md).

   If you need access to Gateway runtime logs (operated by Aembit), contact your Aembit representative.

## Relationship to the MCP Authorization Server

The MCP Identity Gateway uses Aembit’s existing authorization capabilities behind the scenes to:

* Authenticate users via your configured Identity Provider (IdP),
* Evaluate Aembit Access Policies, and
* Orchestrate OAuth 2.0 Authorization Code (3LO) flows to MCP servers.

You **don’t** need to deploy or configure the standalone **Aembit MCP Authorization Server** product to use the MCP Identity Gateway.

* The public MCP Authorization Server docs ([Set up the MCP Authorization Server](../../deploy-install/mcp-authorization-server/overview.md)) describe a **separate control-plane deployment** where Aembit acts as the official Authorization Server for your MCP servers.
* In the MCP Identity Gateway flow, Aembit Cloud provides those authorization capabilities and wires them into the Gateway for you.

To use the MCP Identity Gateway, you only configure:

* An **Identity Provider** in your Aembit Tenant (OIDC or SAML),

* Two **Access Policies**:

  * Client-to-Gateway (AI agent → MCP Gateway)
  * Gateway-to-Server (MCP Gateway → MCP server),

* One **Credential Provider** for each MCP server. Use [MCP User-Based Access Token](../credential-providers/mcp-user-based-access-token.md) when the server requires per-user OAuth credentials, [MCP Enterprise Managed Access Token](../credential-providers/mcp-enterprise-managed-access-token.md) when the server and your OIDC Identity Provider support Enterprise-Managed Authorization, or [OAuth 2.0 Authorization Code](../credential-providers/oauth-authorization-code.md) for shared credentials.

You **shouldn’t** follow the “Set up the MCP Authorization Server” steps unless you’re explicitly using Aembit as a standalone MCP Authorization Server for your own MCP servers.

## Example end-to-end scenario

1. An administrator configures the Gateway and access policies in Aembit
2. A user opens an AI agent (such as Claude), which connects through the Gateway
3. The user queries an MCP server through the agent
4. An auditor reviews logs to see which user and agent accessed which MCP server and when

## Next steps

* [Connect Microsoft Copilot Studio](../../deploy-install/mcp-identity-gateway/connect-copilot-studio.md) - Connect Copilot Studio agents to your MCP servers through the Gateway
* [MCP Identity Gateway reference](../../deploy-install/mcp-identity-gateway/reference-mcp-gateway.md) - Token formats, proxied MCP methods, session management, connectivity, and workload events
* [Client workload identification](client-workload-identification.md) - How the Gateway identifies users in multi-user deployments
* [Audit and report on Workload activity](../../audit-report/overview.md) - Review MCP request and response events for your Tenant
