---
type: explanation
title: "About the MCP Enterprise Managed Access Token Credential Provider"
description: "How the MCP Enterprise Managed Access Token Credential Provider exchanges a user's corporate SSO identity for an MCP server access token without a per-user consent prompt"
resource: https://docs.aembit.io/user-guide/access-policies/credential-providers/about-mcp-enterprise-managed-access-token/
tags: ["credential-provider", "access-policy"]
timestamp: 2026-09-22T15:44:57-07:00
---

# About the MCP Enterprise Managed Access Token Credential Provider

The MCP Enterprise Managed Access Token Credential Provider gives the [MCP Identity Gateway](../../deploy-install/mcp-identity-gateway/overview.md) a per-user access token for a [Model Context Protocol (MCP)](../../deploy-install/about-mcp.md) server. Aembit exchanges the user’s corporate single sign-on (SSO) identity for the MCP server token. The exchange follows the MCP specification’s Enterprise-Managed Authorization flow. The corporate identity provider decides which users and AI agents reach which MCP servers, and users never see a consent prompt for the server.

Use this Credential Provider with the MCP Identity Gateway when both the MCP server and your OpenID Connect (OIDC) identity provider support Enterprise-Managed Authorization.

See [Configure MCP Enterprise Managed Access Token](mcp-enterprise-managed-access-token.md) to create one, and [Prepare the IdP for enterprise-managed access](mcp-enterprise-managed-access-token-idp.md) for the identity provider work that comes first.

## How it works

Enterprise-Managed Authorization replaces the user’s consent prompt with two token requests that Aembit Cloud makes on the user’s behalf. The first request asks the corporate identity provider for an identity assertion, a JSON Web Token (JWT) that vouches for the user to one MCP server. The second request presents that assertion to the MCP server’s authorization server in exchange for an access token.

![Sequence diagram showing how Aembit Cloud exchanges a user's SSO identity for an MCP server access token and the MCP Identity Gateway injects it](https://docs.aembit.io/d2/docs/user-guide/access-policies/credential-providers/about-mcp-enterprise-managed-access-token-0.svg)

1. **Sign-in.** The user signs in to Aembit through the corporate OIDC Identity Provider. Because the Identity Provider grants the `offline_access` scope, the sign-in returns a refresh token as well as an ID token, and Aembit Cloud stores both for the user in encrypted form. Aembit refreshes the stored ID token in the background so it stays valid between sign-ins.

2. **Access Policy evaluation.** An AI agent sends an MCP request to the MCP Identity Gateway. The Gateway identifies the agent and the user, and Aembit Cloud evaluates the Gateway-to-Server Access Policy as it does for every request.

3. **Credential retrieval.** When the Access Policy allows the request, the Credential Provider obtains an access token in two token requests:

   * The **identity assertion request** goes to the corporate Identity Provider’s token endpoint. Aembit presents the user’s ID token in an OAuth 2.0 token exchange (RFC 8693) and receives an identity assertion whose audience is the MCP server’s authorization server.
   * The **access token request** goes to the MCP server’s authorization server. Aembit presents the identity assertion as a JWT bearer grant (RFC 7523) together with the Client ID and scopes from the Credential Provider, and receives an MCP access token for the user.

4. **Credential injection.** The Gateway forwards the MCP request to the MCP server with the access token in the header the Server Workload’s authentication scheme specifies, as for any other token-based Credential Provider.

5. **Cache.** Aembit Cloud caches the access token for the user for up to five minutes, or until shortly before the token expires, and reuses it for later requests from the same user to the same MCP server.

Aembit Cloud makes both token requests. The MCP Identity Gateway never receives the user’s ID token, and the AI agent never receives the MCP access token.

## When to use this type

**Use MCP Enterprise Managed Access Token** when:

* You are configuring a **Gateway-to-Server** Access Policy for the [MCP Identity Gateway](../../deploy-install/mcp-identity-gateway/overview.md) and users must not see a per-server consent prompt
* Your users sign in to Aembit through an OIDC Identity Provider that supports token exchange, such as Okta
* The MCP server supports Enterprise-Managed Authorization

**Use [MCP User-Based Access Token](about-mcp-user-based-access-token.md) instead** when:

* The MCP server doesn’t support Enterprise-Managed Authorization
* Your users sign in through a SAML Identity Provider, or through an OIDC Identity Provider without token exchange
* Each user should authorize the MCP server for themselves

| Consideration     | MCP User-Based Access Token               | MCP Enterprise Managed Access Token            |
| ----------------- | ----------------------------------------- | ---------------------------------------------- |
| Who authorizes    | Each user, at the server’s consent prompt | The corporate identity provider, once          |
| Consent prompt    | One per user for each MCP server          | None                                           |
| Identity Provider | Any Aembit sign-in method                 | OIDC with token exchange, such as Okta         |
| MCP server        | OAuth authorization with per-user consent | Enterprise-Managed Authorization support       |
| Token issued by   | The MCP server, after the user consents   | The MCP server, for the IdP identity assertion |

## Role in MCP Identity Gateway

The [MCP Identity Gateway](../../deploy-install/mcp-identity-gateway/overview.md) requires a Credential Provider for its Gateway-to-Server Access Policy. MCP Enterprise Managed Access Token is one of two per-user options for that policy, alongside [MCP User-Based Access Token](about-mcp-user-based-access-token.md). Both keep credentials isolated per user while every user shares the same Access Policy.

* **Agent-to-Gateway** stays the same. The AI agent holds a token that identifies the agent and the user, valid only against the MCP Identity Gateway.
* **Gateway-to-MCP server** uses the exchanged access token. The Credential Provider retrieves it for the user behind each request, and the Gateway never exposes it to the agent.

Each Gateway-to-Server Access Policy takes one Credential Provider of this type. A tenant can run both per-user types at once. One Access Policy can use MCP Enterprise Managed Access Token for a server that supports Enterprise-Managed Authorization. Another can use MCP User-Based Access Token for a server that doesn’t.

For the full architectural explanation, see [MCP Identity Gateway concepts](../../deploy-install/mcp-identity-gateway/concepts-mcp-gateway.md).

## Requirements and limitations

* **OIDC only.** Users must sign in to Aembit through an [OIDC Identity Provider](../../administration/identity-providers/create-idp-oidc.md). SAML Identity Providers aren’t supported for this flow.
* **`offline_access` scope.** The OIDC Identity Provider must grant the `offline_access` scope so that Aembit receives a refresh token. Users who signed in before you added the scope must sign out and sign in again before the Credential Provider can obtain a token for them.
* **Identity provider and MCP server support.** The corporate identity provider must support OAuth 2.0 token exchange for the MCP server, and the MCP server must support Enterprise-Managed Authorization. Check the MCP server vendor’s documentation for whether, and on which plan, the server supports it.
* **One SSO identity per user.** Aembit stores one SSO identity for each user, captured at sign-in. A user with more than one corporate account uses the account they signed in to Aembit with.
* **Endpoints are static after Discover.** **Discover** fills the Credential Provider’s authorization, token, and introspection endpoints from the MCP server’s metadata when you create it. If the MCP server changes its metadata later, run **Discover** again and save the Credential Provider.

## Related topics

* [Configure MCP Enterprise Managed Access Token](mcp-enterprise-managed-access-token.md) - Step-by-step configuration guide
* [Prepare the IdP for enterprise-managed access](mcp-enterprise-managed-access-token-idp.md) - Identity Provider changes that come before the Credential Provider
* [About MCP User-Based Access Tokens](about-mcp-user-based-access-token.md) - The per-user alternative that uses each user’s own consent
* [MCP Identity Gateway setup](../mcp-identity-gateway/setup-mcp-gateway.md) - How to configure Gateway-to-Server Access Policies that use this Credential Provider
* [MCP Identity Gateway concepts](../../deploy-install/mcp-identity-gateway/concepts-mcp-gateway.md) - How the MCP Identity Gateway uses per-user credentials
