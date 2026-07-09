---
type: explanation
title: "MCP Authorization Server concepts"
description: "Conceptual deep-dive of Aembit's MCP Authorization Server including access control, client authentication, token handling, and URL configuration."
resource: https://docs.aembit.io/ai-guide/mcp/authorization-server/concepts-mcp-auth-server/
tags: [authorization-server, mcp]
timestamp: 2026-06-30T15:16:59-04:00
---

# MCP Authorization Server concepts

This page covers the key concepts you need to understand when working with the Aembit Model Context Protocol (MCP) Authorization Server. For setup instructions, see [Set up the MCP Authorization Server](setup-mcp-auth-server.md).

## Access control

[Section titled “Access control”](#access-control)

Aembit’s MCP Authorization Server uses Aembit Access Policies**Access Policy**: Access Policies define, enforce, and audit access between Client and Server Workloads by cryptographically verifying workload identity and contextual factors rather than relying on static secrets.[Learn more](../../../get-started/concepts/access-policies.md) to control access. An Access Policy connects these components to answer key questions during authorization:

* **Client Workload**Client Workload**: Client Workloads represent software applications, scripts, or automated processes that initiate access requests to Server Workloads, operating autonomously without direct user interaction.[Learn more](../../../get-started/concepts/client-workloads.md)** - Identifies which MCP client is requesting access. For MCP, the redirect URI from Dynamic Client Registration serves as the client identifier. This enables granular policies per client application (such as Gemini CLI or MCP Jam).

* **Server Workload**Server Workload**: Server Workloads represent target services, APIs, databases, or applications that receive and respond to access requests from Client Workloads.[Learn more](../../../get-started/concepts/server-workloads.md)** - Identifies which MCP server the client wants to access. The Server Workload configuration (host, port, path) must align with your MCP server’s URL and the `resource` parameter. See [URL configuration alignment](#url-configuration-alignment) for details.

* **Trust Provider**Trust Provider**: Trust Providers validate Client Workload identities through workload attestation, verifying identity claims from the workload's runtime environment rather than relying on pre-shared secrets.[Learn more](../../../get-started/concepts/trust-providers.md)** - Validates user identity during the authorization flow. For MCP with human authentication, the OIDC ID Token Trust Provider matches claims (issuer, audience, subject) from your identity provider to verify the user. When [Enforce SSO is off](#how-no-user-auth-authorization-works) on the Client Workload, no Trust Provider is needed.

* **Access Conditions**Access Condition**: Access Conditions add dynamic, context-aware constraints to authorization by evaluating circumstances like time, location, or security posture to determine whether to grant access.[Learn more](../../../get-started/concepts/access-conditions.md)** - Adds context-based restrictions such as time-of-day or geolocation. For geolocation conditions, both the MCP client’s IP and the user’s browser IP must satisfy the restriction.

* **Credential Provider**Credential Provider**: Credential Providers obtain the specific access credentials—such as API keys, OAuth tokens, or temporary cloud credentials—that Client Workloads need to authenticate to Server Workloads.[Learn more](../../../get-started/concepts/credential-providers.md)** - Generates the access token that the MCP client uses to authenticate with the MCP server. The token includes an audience claim matching the Server Workload and uses the configured signing algorithm (ES256 (default) or RSA).

For step-by-step configuration, see [Set up the MCP Authorization Server](setup-mcp-auth-server.md).

### Left-side and right-side auth

[Section titled “Left-side and right-side auth”](#left-side-and-right-side-auth)

The Access Policy components are also described using **left-side** and **right-side** terminology. These are terms you’ll encounter in Aembit documentation and support conversations.

* **Left-side auth** - the inbound authentication side. The Trust Provider validates user identity, and the Client Workload identifies which MCP client application is requesting access.
* **Right-side auth** - the outbound credential side. The Credential Provider generates the access token, and the Server Workload defines the MCP server the token targets.

## MCP Authorization Server architecture

[Section titled “MCP Authorization Server architecture”](#mcp-authorization-server-architecture)

This diagram shows how Aembit’s MCP Authorization Server fits into the MCP ecosystem. Aembit sits between MCP clients and MCP servers, applying Access Policy controls to authorization flows.

The authorization flow differs depending on your configuration. When **Enforce SSO** is on for your Client Workload (the default), users authenticate through your identity provider. Select your protocol to see the flow. When **Enforce SSO** is off, the flow skips user authentication entirely.

* OIDC

  ![MCP Authorization Server architecture with OIDC identity provider](https://docs.aembit.io/d2/docs/ai-guide/mcp/authorization-server/concepts-mcp-auth-server-0.svg)

* SAML

  ![MCP Authorization Server architecture with SAML identity provider](https://docs.aembit.io/d2/docs/ai-guide/mcp/authorization-server/concepts-mcp-auth-server-1.svg)

* No user auth

  ![MCP Authorization Server architecture without user authentication (Enforce SSO disabled)](https://docs.aembit.io/d2/docs/ai-guide/mcp/authorization-server/concepts-mcp-auth-server-2.svg)

### How OIDC authorization works

[Section titled “How OIDC authorization works”](#how-oidc-authorization-works)

1. **Initiate command** - The user runs a command in their MCP client (like Gemini CLI)
2. **Register and request auth** - The MCP client registers with Aembit and requests authorization
3. **Redirect to IdP** - Aembit redirects the user’s browser to the OIDC identity provider
4. **Authenticate** - The user signs in with their corporate credentials
5. **Return ID token** - The identity provider returns an OIDC ID token to Aembit’s Trust Provider
6. **Verify** - The Trust Provider validates the ID token claims (issuer, audience, subject)
7. **Check** - Access Conditions check contextual factors (time, location)
8. **Issue access token** - The Credential Provider generates a JWT access token
9. **Access with bearer token** - The MCP client uses the token to access the protected MCP server

### How SAML authorization works

[Section titled “How SAML authorization works”](#how-saml-authorization-works)

1. **Initiate command** - The user runs a command in their MCP client (like Gemini CLI)
2. **Register and request auth** - The MCP client registers with Aembit and requests authorization
3. **Redirect to IdP** - Aembit redirects the user’s browser to the SAML identity provider
4. **Authenticate** - The user signs in with their corporate credentials
5. **Return SAML assertion** - The identity provider returns a SAML assertion to Aembit’s Trust Provider
6. **Verify** - The SAMLv2 Response Trust Provider validates the SAML assertion
7. **Check** - Access Conditions check contextual factors (time, location)
8. **Issue access token** - The Credential Provider generates a JWT access token
9. **Access with bearer token** - The MCP client uses the token to access the protected MCP server

### How no-user-auth authorization works

[Section titled “How no-user-auth authorization works”](#how-no-user-auth-authorization-works)

1. **Initiate command** - The user runs a command in their MCP client (like Gemini CLI)
2. **Register and request auth** - The MCP client registers with Aembit and requests authorization
3. **Check** - Access Conditions check contextual factors (time, location) using the MCP client’s IP address
4. **Issue access token** - The Credential Provider generates a JWT access token
5. **Access with bearer token** - The MCP client uses the token to access the protected MCP server

No user authentication or Trust Provider

When Enforce SSO is off on the Client Workload, the MCP Authorization Server skips user authentication entirely. There is no browser redirect, no identity provider interaction, and no Trust Provider validation. The Credential Provider still generates the access token, and Access Conditions still apply.

For guidance on choosing between OIDC and SAML, see [MCP Authorization Server overview](overview.md#choosing-between-oidc-and-saml).

## Client authentication

[Section titled “Client authentication”](#client-authentication)

Aembit’s MCP Authorization Server identifies MCP clients through two OAuth mechanisms:

Dynamic Client Registration (DCR)**Dynamic Client Registration**: An OAuth mechanism that allows MCP clients to register with the Authorization Server at runtime without pre-configuration, receiving unique credentials for subsequent authorization requests.[Learn more](concepts-mcp-auth-server.md#client-registration) and Client ID Metadata Documents (CIMD)**Client ID Metadata Document**: A JSON document that an MCP client hosts at an HTTPS URL, containing its client\_id, client\_name, and redirect\_uris. When a client presents that URL as its OAuth client\_id, the Authorization Server fetches and validates the document, so the client can authenticate without Dynamic Client Registration (DCR).[Learn more(opens in new tab)](https://modelcontextprotocol.io/specification/2025-11-25/basic/authorization#client-id-metadata-documents). This section covers both, along with how redirect URIs identify clients.

### Client registration

[Section titled “Client registration”](#client-registration)

With Dynamic Client Registration (DCR), MCP clients register themselves at runtime by sending a registration request to the `/register` endpoint. The Authorization Server returns a unique `client_id` for subsequent authorization requests.

For detailed DCR mechanics, see the [MCP authorization specification](https://modelcontextprotocol.io/specification/2025-06-18/basic/authorization#2-3-2-dynamic-client-registration-dcr).

### Client ID Metadata Documents

[Section titled “Client ID Metadata Documents”](#client-id-metadata-documents)

With CIMD, an MCP client hosts a metadata document at an HTTPS URL and presents that URL as its `client_id`. When the client presents a URL-formatted `client_id`, Aembit fetches the document from that URL and validates the client’s metadata. Aembit processes a document only when an administrator has registered its URL on a Client Workload, which ensures only clients you register can obtain access tokens.

To configure a Client Workload that uses CIMD, see [CIMD Client ID](../../../user-guide/access-policies/client-workloads/identification/cimd-client-id.md).

### Redirect URIs

[Section titled “Redirect URIs”](#redirect-uris)

In OAuth 2.1, a redirect URI is the callback URL where the Authorization Server sends users after they authenticate. When an MCP client registers through Dynamic Client Registration, it provides its redirect URI, which tells the Authorization Server where to send the authorization code after successful authentication.

For details on the OAuth 2.1 redirect flow, see [RFC 6749](https://datatracker.ietf.org/doc/html/rfc6749#section-3.1.2).

#### Redirect URIs as Client Workload identifiers

[Section titled “Redirect URIs as Client Workload identifiers”](#redirect-uris-as-client-workload-identifiers)

In Aembit Access Policies, the redirect URI serves a dual purpose. It’s both the OAuth callback URL and the identifier for your Client Workload. This enables granular access policies based on which MCP clients are requesting access. For step-by-step configuration, see [Redirect URI identifier](../../../user-guide/access-policies/client-workloads/identification/redirect-uri.md).

For example, if Gemini CLI registers with `http://localhost:7777/oauth/callback`, you would configure a Client Workload with the Redirect URI identifier type set to this value. This ensures only authorized MCP clients can obtain access tokens for your protected MCP servers.

#### Common redirect URI patterns

[Section titled “Common redirect URI patterns”](#common-redirect-uri-patterns)

Different MCP clients use different redirect URI formats depending on whether they run locally or remotely.

**Local development:**

| MCP client | Redirect URI                           |
| ---------- | -------------------------------------- |
| MCP Jam    | `http://localhost:6274/oauth/callback` |
| Gemini CLI | `http://localhost:7777/oauth/callback` |

**Remote or cloud-hosted:**

| MCP client     | Redirect URI                                  |
| -------------- | --------------------------------------------- |
| Claude Desktop | `https://claude.ai/api/mcp/auth_callback`     |
| Custom web app | `https://your-app.example.com/oauth/callback` |

Port numbers for loopback URIs

For loopback IP addresses (localhost and 127.0.0.1), Aembit ignores the port number during redirect URI matching. This means a Client Workload configured with `http://localhost:7777/oauth/callback` will match requests from any port like `http://localhost:8080/oauth/callback` or `http://localhost:3000/oauth/callback`. Wildcards aren’t supported in redirect URIs.

## Token handling

[Section titled “Token handling”](#token-handling)

This section covers token issuance, validation, and refresh behavior in the MCP authorization flow.

### Token audience

[Section titled “Token audience”](#token-audience)

The `audience` claim is a standard [JWT claim](https://datatracker.ietf.org/doc/html/rfc7519#section-4.1.3) that identifies the intended recipient of a token - in this case, your MCP server.

In Aembit, you configure the audience value in your Credential Provider settings. This value must match exactly what your MCP server expects in its token verifier configuration. For example, `https://mcp.acme-corp.example.com` and `https://mcp.acme-corp.example.com/` (with trailing slash) are different values and cause validation to fail.

### Credential acquisition

[Section titled “Credential acquisition”](#credential-acquisition)

Aembit’s MCP Authorization Server supports acquiring credentials for downstream services through Aembit’s Credential Provider system.

#### Supported credential types

[Section titled “Supported credential types”](#supported-credential-types)

When an MCP client successfully authenticates, the Authorization Server provides OIDC ID Tokens (JWT tokens) for accessing protected MCP servers.

Configure the credential type in your Access Policy’s Credential Provider settings. See [Credential Providers](../../../user-guide/access-policies/credential-providers/overview.md) for available options.

### Token refresh

[Section titled “Token refresh”](#token-refresh)

OIDC ID Token and Aembit Access Token Credential Providers support optional refresh token configuration for MCP Authorization Server flows. When you enable refresh token support on a Credential Provider, the MCP Authorization Server returns a refresh token alongside the access token during OAuth token requests. Clients can exchange a refresh token for a new access token and a new refresh token, maintaining an active session without completing a new authorization flow.

Key behaviors:

* **Single-use tokens** - Each refresh token works for a single exchange. Every exchange returns a new refresh token.
* **Absolute Token Lifetime** - Controls the total duration during which refresh tokens remain valid after the initial token issuance. This lifetime doesn’t reset with each exchange.
* **Lifetime constraint** - The Absolute Token Lifetime must be greater than the access token Lifetime to allow at least one token exchange.
* **MCP-only** - The MCP Authorization Server only returns refresh tokens for MCP flows. Agent Proxy flows don’t return refresh tokens, even when you enable this option on the Credential Provider.

To configure refresh token support, see [OIDC ID Token](../../../user-guide/access-policies/credential-providers/oidc-id-token.md) or [Aembit Access Token](../../../user-guide/access-policies/credential-providers/aembit-access-token.md).

## URL configuration alignment

[Section titled “URL configuration alignment”](#url-configuration-alignment)

Three URLs must align for the MCP authorization flow to succeed:

| Component  | Configuration                      | Example value                           |
| ---------- | ---------------------------------- | --------------------------------------- |
| MCP Client | Target URL                         | `https://mcp.acme-corp.example.com/mcp` |
| MCP Server | `resource`                         | `https://mcp.acme-corp.example.com`     |
| Aembit     | Server Workload (host, port, path) | `mcp.acme-corp.example.com:443/mcp`     |

The `resource` parameter omits the path (`/mcp`) because it identifies the server origin for token audience matching, not the specific endpoint. The MCP client and Aembit Server Workload include the full path to specify the MCP endpoint mount location. If these URLs don’t match, you encounter [URL mismatch errors](troubleshooting-mcp-auth-server.md#url-mismatch-errors).

### Why alignment matters

[Section titled “Why alignment matters”](#why-alignment-matters)

During the OAuth flow:

1. The MCP client connects to your MCP server using its target URL
2. The MCP client receives protected resource metadata (including the `resource` parameter) from your MCP server
3. Aembit verifies the `resource` parameter matches the Server Workload configuration in the Access Policy
4. The MCP client requests a token from the Aembit MCP Authorization Server
5. The Aembit MCP Authorization Server validates the request against your Server Workload configuration

A mismatch at any step causes the authorization flow to fail.

![URL configuration alignment between MCP client, MCP server, and Aembit](https://docs.aembit.io/d2/docs/ai-guide/mcp/authorization-server/concepts-mcp-auth-server-3.svg)

## Related resources

[Section titled “Related resources”](#related-resources)

* [MCP Authorization Server overview](overview.md)
* [Set up the MCP Authorization Server](setup-mcp-auth-server.md)
* [MCP Authorization Server reference](reference-mcp-auth-server.md)
* [Troubleshooting](troubleshooting-mcp-auth-server.md)
* [MCP Identity Gateway concepts](../identity-gateway/concepts-mcp-gateway.md) - How left-side and right-side auth apply in the Gateway context
