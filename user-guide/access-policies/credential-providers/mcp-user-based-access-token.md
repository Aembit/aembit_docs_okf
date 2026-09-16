---
type: how-to
title: "Configure MCP User-Based Access Token Credential Provider"
description: "How to create and use an MCP User-Based Access Token Credential Provider for user-scoped OAuth credentials with MCP servers"
resource: https://docs.aembit.io/user-guide/access-policies/credential-providers/mcp-user-based-access-token/
interface: web-ui
tags: ["credential-provider", "access-policy"]
timestamp: 2026-09-15T18:18:13-07:00
---

# Configure MCP User-Based Access Token Credential Provider

The MCP User-Based Access Token Credential Provider retrieves and manages OAuth 2.0 credentials on a per-user basis for [Model Context Protocol (MCP)](../../deploy-install/about-mcp.md) server access. The standard [OAuth 2.0 Authorization Code](oauth-authorization-code.md) Credential Provider stores tokens for the Aembit administrator who authorizes the integration. This type stores tokens for each end user who authenticates through the MCP authorization flow.

Use this Credential Provider with the [MCP Identity Gateway](../../deploy-install/mcp-identity-gateway/overview.md), where multiple users access MCP servers through a shared Gateway. Each user completes their own OAuth consent flow, and Aembit stores and rotates their tokens individually.

For background on how this type works and when to use it, see [About MCP User-Based Access Tokens](about-mcp-user-based-access-token.md).

## Create an MCP user-based access token Credential Provider

To create an MCP User-Based Access Token Credential Provider, follow these steps:

1. Log into your Aembit Tenant, and go to **Credential Providers** in the left sidebar.

2. (Optional) In the top right corner, select the [Resource Set](../../administration/resource-sets/overview.md) that you want this Credential Provider to reside.

3. Click **+ New**, which displays the Credential Provider pop out menu.

4. Enter a **Name** and optional **Description**.

5. Under **Credential Type**, select **MCP User-Based Access Token**, revealing more fields.

6. Fill out the remaining fields:

   1. **MCP Server URL** - The base URL of the target MCP server (for example, `https://dbc-a1044c90-95d9.cloud.databricks.com/api/2.0/mcp`). Click **Discover** to auto-populate the Authorization URL and Token URL from the server’s metadata.

   2. **Callback URL** - Auto-generated and read-only. This is the URL Aembit uses to receive OAuth authorization codes during the user consent flow. It takes the form `https://<tenantId>.id.<region>.aembit.io/mcp-auth/userauth/<cp-id>/callback`. Aembit assigns the final value when you click **Save**, so save the Credential Provider before you copy it. Register this URL with the MCP server’s own OAuth authorization server if it requires callback URL registration.

   3. **Client ID** - The OAuth client identifier for the MCP server. Obtain this from the MCP server vendor’s configuration or by using a dynamic client registration endpoint.

   4. **Client Secret** - The OAuth client secret, if required by the MCP server.

   5. **Scopes** - Space-separated list of OAuth scopes (for example, `all-apis offline_access`). **Discover** may auto-populate this field.

   6. **Resource** - (Optional) The resource parameter for the token request. Required only if the MCP server can’t use the auto-generated value. Some providers, such as Microsoft, require this field.

   7. **Authorization URL** - The endpoint where the user authenticates and grants consent. Auto-populated by **Discover** if the MCP server supports metadata discovery.

   8. **Token URL** - The endpoint that exchanges authorization codes for access tokens. Auto-populated by **Discover**.

   9. **Introspection URL** - (Optional) The token introspection endpoint, if supported by the MCP server.

   10. **PKCE Required** - Enable this if the MCP server requires Proof Key for Code Exchange (PKCE). Recommended for security.

   11. **Additional Parameters** - (Optional) Key-value pairs for custom parameters to include in the token request.

   The form should look similar to the following screenshot: ![MCP User-Based Access Token Credential Provider form](https://docs.aembit.io/_astro/cp-mcp-user-based-access-token-form.asZBlF4G_2ogrjC.webp)

7. Click **Save**.

   Aembit displays the new Credential Provider in the list of Credential Providers.

> **Using Discover**
>
> After entering the **MCP Server URL**, click **Discover** to auto-populate the Authorization URL, Token URL, and Scopes from the MCP server’s OAuth metadata. This works with MCP servers that publish a standard `/.well-known/oauth-authorization-server` metadata document.
>
> If discovery fails, enter the Authorization URL and Token URL manually using values from the MCP server vendor’s documentation.

> **Existing Credential Providers keep their original callback URL**
>
> Aembit changed the callback URL format for MCP User-Based Access Token Credential Providers. Providers created before that change keep their original callback URL, which takes the form `https://<tenantId>.mcp.<region>.aembit.io/userauth/<cp-id>/callback`.
>
> Updating a Credential Provider doesn’t regenerate its callback URL, so you may see either format depending on when you created the provider. Both formats remain valid, and you don’t need to take any action for existing providers.
>
> Always copy the exact value shown in the Credential Provider rather than constructing the URL yourself, and register that value with the MCP server’s own OAuth authorization server.

## Related topics

* [About MCP User-Based Access Tokens](about-mcp-user-based-access-token.md) - How this Credential Provider works and when to use it
* [MCP Identity Gateway setup](../mcp-identity-gateway/setup-mcp-gateway.md) - How to configure Gateway-to-Server Access Policies that use this Credential Provider
* [MCP Identity Gateway concepts](../../deploy-install/mcp-identity-gateway/concepts-mcp-gateway.md) - How the MCP Identity Gateway uses per-user credentials
* [OAuth 2.0 Authorization Code](oauth-authorization-code.md) - The standard OAuth flow for administrator-authorized credentials
* [Credential Providers overview](overview.md) - All available Credential Provider types
