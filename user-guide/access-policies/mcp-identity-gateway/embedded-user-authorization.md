---
type: explanation
title: "Embedded user authorization"
description: "How your application signs a person in and connects an MCP server for them without Aembit pages, then uses that access at runtime."
resource: https://docs.aembit.io/user-guide/access-policies/mcp-identity-gateway/embedded-user-authorization/
tags: ["mcp-identity-gateway", "access-policy"]
timestamp: 2026-11-11T10:31:46-08:00
---

# Embedded user authorization

Use embedded user authorization when your application has its own UI, such as a portal or AI agent platform.

During an embedded user authorization flow, the person signs in to their Identity Provider and connects one MCP server. At the end of the flow, your application gets an MCP Identity Gateway access token that acts as that person, and calls MCP Identity Gateway with it. MCP Identity Gateway presents the person’s own credential to the MCP server.

Your application must run a standard OAuth authorization code flow with PKCE against Aembit. The people who use your application must sign in with SSO through an OpenID Connect (OIDC) Identity Provider. The Client Workload that represents your application must have **Enforce SSO** on.

## When to use embedded user authorization

* People connect MCP servers without leaving your application.
* Your application decides which MCP server a person authorizes.

## How embedded user authorization works

### What the person sees

Connecting an MCP server takes the person through their Identity Provider’s sign-in page and the MCP server’s consent page.

1. Your application starts the flow, and the person’s browser goes to the sign-in page of the person’s Identity Provider. The Identity Provider might skip that page when the person already has a session there.
2. The browser goes to the MCP server’s consent page, where the person approves access.
3. The browser returns to your application.

![Pages the person sees during embedded user authorization: your application, the Identity Provider's sign-in page, the MCP server's consent page, and back to your application](https://docs.aembit.io/d2/docs/user-guide/access-policies/mcp-identity-gateway/embedded-user-authorization-0.svg)

Aembit shows no page of its own along the way, such as an Identity Provider picker or a status page. Aembit sends the person to the MCP server each time your application runs the flow, even when Aembit already holds a token for that person and MCP server. Most MCP servers show their consent page on every run, and some skip it for a person who already consented. See [Consent at the MCP server](reference-embedded-user-authorization.md#consent-at-the-mcp-server).

### What your application does

Your application starts the flow by sending the person’s browser to Aembit’s authorize endpoint, naming the MCP server the person is authorizing. Every redirect in the following diagram passes through the person’s browser.

![Sequence of embedded user authorization between your application, Aembit Cloud, the Identity Provider, and the MCP server's authorization server](https://docs.aembit.io/d2/docs/user-guide/access-policies/mcp-identity-gateway/embedded-user-authorization-1.svg)

After the flow, your application calls MCP Identity Gateway with the access token. MCP Identity Gateway finds the person’s stored MCP server token and presents it to the MCP server.

For every parameter, rule, and error in this flow, see [Embedded user authorization reference](reference-embedded-user-authorization.md).

### Access Policy requirements

Each Access Policy in the flow requires a specific Credential Provider.

* The Client-to-Gateway Policy needs an OIDC ID Token Credential Provider, with its **Audience** set to the MCP Identity Gateway URL.
* The Gateway-to-Server Policy needs exactly one active MCP User-Based Access Token Credential Provider and no active SSO Access Token Passthrough Credential Provider. Exactly one Gateway-to-Server Policy must match the MCP server the person is authorizing.

### Token lifetimes

When the MCP server issues a refresh token, Aembit refreshes the stored MCP server token on its own for as long as the MCP server honors that refresh token.

The access token your application gets lasts for the lifetime the Client-to-Gateway Policy’s Credential Provider sets. When that Credential Provider has **Enable Refresh Token Support** turned on, your application also gets a refresh token. Your application exchanges the refresh token for a new access token until the Credential Provider’s **Absolute Token Lifetime** ends. Without a valid refresh token, your application must run the flow again when the access token expires. Aembit then sends the person back to the MCP server.

## Limitations

* Each run of the flow authorizes one MCP server. A person who needs more than one MCP server goes through the flow once for each.
* Your application names the MCP server in the `mcp_server_url` parameter, an Aembit addition that neither OAuth nor the MCP authorization specification defines. Your application’s OAuth client must be able to add a custom parameter to the authorize request.

## See also

* [Set up embedded user authorization](setup-embedded-user-authorization.md)
* [Embedded user authorization reference](reference-embedded-user-authorization.md)
* [MCP Identity Gateway concepts](../../deploy-install/mcp-identity-gateway/concepts-mcp-gateway.md)
* [About MCP User-Based Access Tokens](../credential-providers/about-mcp-user-based-access-token.md)
