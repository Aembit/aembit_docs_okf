---
type: how-to
title: "Configure MCP Enterprise Managed Access Token"
description: "Create an MCP Enterprise Managed Access Token Credential Provider so the MCP Identity Gateway can obtain per-user MCP server tokens from your corporate identity provider"
resource: https://docs.aembit.io/user-guide/access-policies/credential-providers/mcp-enterprise-managed-access-token/
interface: web-ui
tags: ["credential-provider", "access-policy"]
timestamp: 2026-09-22T15:44:57-07:00
---

# Configure MCP Enterprise Managed Access Token

The MCP Enterprise Managed Access Token Credential Provider gives the [MCP Identity Gateway](../../deploy-install/mcp-identity-gateway/overview.md) a per-user access token for a [Model Context Protocol (MCP)](../../deploy-install/about-mcp.md) server. Aembit exchanges the user’s corporate single sign-on (SSO) identity for the MCP server token. The exchange follows the MCP specification’s Enterprise-Managed Authorization flow. The corporate identity provider decides which users and AI agents reach which MCP servers, and users never see a consent prompt for the server.

Use this Credential Provider with the MCP Identity Gateway when both the MCP server and your OpenID Connect (OIDC) identity provider support Enterprise-Managed Authorization.

See [About the MCP Enterprise Managed Access Token Credential Provider](about-mcp-enterprise-managed-access-token.md) for how the token exchange works and when to choose this type.

## Before you start

* **An OIDC Identity Provider prepared for token exchange.** Complete the [`offline_access` scope](mcp-enterprise-managed-access-token-idp.md#add-the-offline_access-scope-in-aembit) and [token exchange](mcp-enterprise-managed-access-token-idp.md#enable-token-exchange-on-the-okta-application) sections of [Prepare the Identity Provider for enterprise-managed access](mcp-enterprise-managed-access-token-idp.md) first, and return to [Register the AI agent in Okta](mcp-enterprise-managed-access-token-idp.md#register-the-ai-agent-in-okta) after **Discover** returns a Client ID. The Credential Provider’s **Corporate Identity Provider** list shows only active OIDC Identity Providers.
* **The Identity Providers permission.** Your Aembit role needs it to load the Corporate Identity Provider list. Without it, the form shows “To configure the Corporate Identity Provider, you must have the ‘Identity Providers’ permission.”
* **An MCP server that supports Enterprise-Managed Authorization.** Check the MCP server vendor’s documentation for whether, and on which plan, the server supports it.
* **An MCP Server URL reachable from the internet.** Aembit rejects a URL that resolves to an internal host.

## Create the Credential Provider

To create an MCP Enterprise Managed Access Token Credential Provider, follow these steps:

1. Log into your Aembit Tenant, and go to **Credential Providers** in the left sidebar.

2. (Optional) In the top right corner, select the [Resource Set](../../administration/resource-sets/overview.md) that you want this Credential Provider to reside.

3. Click **+ New**, which displays the Credential Provider pop out menu.

4. Enter a **Name** and optional **Description**.

5. Under **Credential Type**, select **MCP Enterprise Managed Access Token**, revealing more fields.

6. Fill out the remaining fields:

   1. **OIDC Issuer URL** - The issuer identifier of the MCP server’s authorization server (for example, `https://mcp.example.com`). Aembit requests the identity assertion from your Identity Provider with this value as its audience, so it must match the issuer that the Identity Provider trusts for the MCP server.

   2. **MCP Server URL** - The base URL of the target MCP server (for example, `https://mcp.example.com/v1/mcp`). Click **Discover** to fill the Client ID, Scopes, Authorization URL, Token URL, and Introspection URL from the server’s metadata.

   3. **Client ID** - The OAuth client identifier that the MCP server’s authorization server issued for Aembit. **Discover** fills it when the MCP server supports dynamic client registration. The form has no Client Secret field, because the identity assertion authenticates the access token request.

   4. **Scopes** - Space-separated list of OAuth scopes to request from the MCP server. **Discover** may fill this field.

   5. **Authorization URL** - The MCP server’s authorization endpoint, under **Endpoints (Static)**. **Discover** fills it when the MCP server publishes authorization server metadata.

   6. **Token URL** - The endpoint where Aembit presents the identity assertion in exchange for the MCP access token. **Discover** fills it.

   7. **Introspection URL** - (Optional) The MCP server’s token introspection endpoint, if it publishes one. Aembit uses it to determine when an access token expires if the token response doesn’t say.

   8. Leave **Identity Assertion Issuer Mode** at its default, **Corporate Identity Provider**.

   9. **Corporate Identity Provider** - Select the OIDC Identity Provider that your users sign in to Aembit through. The list shows only active OIDC Identity Providers.

7. Click **Save**.

   Aembit displays the new Credential Provider in the list of Credential Providers.

This Credential Provider type has no **Verify** option. Aembit obtains each access token for the user behind a request, so there is no token to verify at setup time.

> **Using Discover**
>
> After entering the **MCP Server URL**, click **Discover**. Aembit reads the MCP server’s resource metadata and authorization server metadata, and registers a client through dynamic client registration when the server offers it. It then fills the Client ID, Scopes, Authorization URL, Token URL, and Introspection URL. For this Credential Provider type, the registration requests the `jwt-bearer` grant type as well as `authorization_code`, so the MCP server issues a Client ID that accepts an identity assertion.
>
> **Discover** overwrites values already in those fields and clears any of them that the metadata doesn’t cover.
>
> If discovery fails, the form shows one of three hints:
>
> * `MCP Server does not support resource metadata discovery`
> * `MCP Server does not support auth metadata discovery`
> * `Dynamic Client Registration failed`
>
> Enter the missing values manually using values from the MCP server vendor’s documentation.

Each Gateway-to-Server Access Policy takes one Credential Provider of this type.

## Next step

Attach the Credential Provider to the Gateway-to-Server Access Policy that fronts the MCP server. See [Create the gateway-to-server Access Policy](../mcp-identity-gateway/setup-mcp-gateway.md#create-the-gateway-to-server-access-policy).

## Related topics

* [About the MCP Enterprise Managed Access Token Credential Provider](about-mcp-enterprise-managed-access-token.md) - How this Credential Provider works and when to use it
* [Prepare the Identity Provider for enterprise-managed access](mcp-enterprise-managed-access-token-idp.md) - Identity Provider changes that come before the Credential Provider
* [MCP Identity Gateway setup](../mcp-identity-gateway/setup-mcp-gateway.md) - How to configure Gateway-to-Server Access Policies that use this Credential Provider
* [MCP Identity Gateway concepts](../../deploy-install/mcp-identity-gateway/concepts-mcp-gateway.md) - How the MCP Identity Gateway uses per-user credentials
* [About MCP User-Based Access Tokens](about-mcp-user-based-access-token.md) - The per-user alternative that uses each user’s own consent
* [Credential Providers overview](overview.md) - All available Credential Provider types
