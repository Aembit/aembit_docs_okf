---
type: reference
title: "MCP Authorization Server reference"
description: "Configuration options, endpoints, and error codes for the Aembit MCP Authorization Server."
resource: https://docs.aembit.io/ai-guide/mcp/authorization-server/reference-mcp-auth-server/
interface: mcp
tags: [authorization-server, mcp]
timestamp: 2026-06-30T15:16:59-04:00
---

# MCP Authorization Server reference

This reference documents the configuration options, endpoints, and error codes for the Aembit Model Context Protocol (MCP) Authorization Server.

## Configuration concepts

[Section titled “Configuration concepts”](#configuration-concepts)

MCP servers require specific configuration to work with Aembit’s MCP Authorization Server. Most settings are standard OAuth concepts from the MCP specification—the exact field names vary by MCP server implementation.

| Concept                      | Purpose                                                | Aembit value                                                                    | Required by |
| ---------------------------- | ------------------------------------------------------ | ------------------------------------------------------------------------------- | ----------- |
| **Authorization Server URL** | Where MCP clients discover OAuth endpoints             | `https://[tenant].mcp.[region].aembit.io`                                       | MCP spec    |
| **Resource Server URL**      | Identifies your MCP server for token audience matching | Your MCP server’s public URL                                                    | MCP spec    |
| **JWKS Endpoint**            | Public keys used for token signature verification      | `https://[tenant].mcp.[region].aembit.io/.well-known/openid-configuration/jwks` | RFC 8414    |
| **Token Issuer**             | OIDC issuer URL of token validation                    | `https://[tenant].id.[region].aembit.io` (note: `.id.` subdomain)               | RFC 8414    |
| **Token Audience**           | Expected audience claim in issued tokens               | Must match your Credential Provider configuration                               | RFC 8707    |
| **Signing Algorithm**        | Algorithm for token signatures                         | `ES256` (default) or RSA                                                        | Aembit      |

### MCP server configuration

[Section titled “MCP server configuration”](#mcp-server-configuration)

Looking for the Aembit MCP Server?

This section covers configuring third-party MCP servers to work with Aembit’s MCP Authorization Server. For information about Aembit’s own MCP Server, see [Aembit MCP Server](../mcp-server/overview.md).

MCP servers must specify their Authorization Server so unauthenticated clients know where to authenticate. Configuration methods and field names vary by implementation—your MCP server may use JSON, YAML, environment variables, or other formats. Use the values from the preceding table and consult your MCP server’s documentation for the correct field names.

For step-by-step configuration instructions, see [Set up the MCP Authorization Server](setup-mcp-auth-server.md#mcp-server-requirements). For environment variable-based configuration, see the [MCP server environment variables reference](env-vars-mcp-auth-server.md).

## Tenant URL patterns

[Section titled “Tenant URL patterns”](#tenant-url-patterns)

Aembit uses different subdomains for different services. When configuring your MCP server, use the correct subdomain for each service.

| Service         | URL pattern                                                                     | Example                                                                      |
| --------------- | ------------------------------------------------------------------------------- | ---------------------------------------------------------------------------- |
| MCP Auth Server | `https://[tenant].mcp.[region].aembit.io`                                       | `https://abc123.mcp.useast2.aembit.io`                                       |
| Token Issuer    | `https://[tenant].id.[region].aembit.io`                                        | `https://abc123.id.useast2.aembit.io`                                        |
| JWKS            | `https://[tenant].mcp.[region].aembit.io/.well-known/openid-configuration/jwks` | `https://abc123.mcp.useast2.aembit.io/.well-known/openid-configuration/jwks` |

Replace `[tenant]` with your Aembit tenant ID and `[region]` with your deployment region (for example, `useast2`).

Token issuer subdomain

The token issuer uses the `.id.` subdomain, not `.mcp.`. Ensure your MCP server’s `issuer` configuration uses the correct subdomain.

## Endpoints

[Section titled “Endpoints”](#endpoints)

| Endpoint                                       | Method | Description                                               |
| ---------------------------------------------- | ------ | --------------------------------------------------------- |
| `/.well-known/openid-configuration`            | GET    | OIDC discovery metadata document                          |
| `<jwks_uri>`                                   | GET    | JWKS public keys, as advertised in the discovery document |
| `<registration_endpoint>`                      | POST   | Dynamic Client Registration (DCR)                         |
| `<authorization_endpoint>` (e.g. `/authorize`) | GET    | OAuth 2.1 authorization endpoint                          |
| `<token_endpoint>` (e.g. `/token`)             | POST   | OAuth 2.1 token endpoint                                  |

All endpoints except `/.well-known/openid-configuration` are discovered via the metadata document and must not be hard-coded; the preceding examples (`/authorize`, `/token`) illustrate typical paths only.

### Endpoint response examples

[Section titled “Endpoint response examples”](#endpoint-response-examples)

#### OAuth authorization server metadata

[Section titled “OAuth authorization server metadata”](#oauth-authorization-server-metadata)

```http
GET /.well-known/oauth-authorization-server
```

Response:

```json
{
  "issuer": "https://[tenant].mcp.[region].aembit.io",
  "authorization_endpoint": "https://[tenant].mcp.[region].aembit.io/connect/authorize",
  "token_endpoint": "https://[tenant].mcp.[region].aembit.io/connect/token",
  "jwks_uri": "https://[tenant].mcp.[region].aembit.io/.well-known/openid-configuration/jwks",
  "registration_endpoint": "https://[tenant].mcp.[region].aembit.io/register",
  "scopes_supported": ["openid", "profile", "email"],
  "response_types_supported": ["code", "token", "id_token", "id_token token", "code id_token", "code token", "code id_token token"],
  "response_modes_supported": ["form_post", "query", "fragment"],
  "grant_types_supported": ["authorization_code"],
  "code_challenge_methods_supported": ["plain", "S256"],
  "client_id_metadata_document_supported": true
}
```

#### Protected resource metadata

[Section titled “Protected resource metadata”](#protected-resource-metadata)

```http
GET <resource_metadata_url>
```

Response:

```json
{
  "resource": "http://your-mcp-server:8000/mcp",
  "authorization_servers": ["https://[tenant].mcp.[region].aembit.io"]
}
```

## HTTP headers

[Section titled “HTTP headers”](#http-headers)

The MCP authorization flow uses these headers:

### Response headers

[Section titled “Response headers”](#response-headers)

| Header             | Description                                                                                   |
| ------------------ | --------------------------------------------------------------------------------------------- |
| `WWW-Authenticate` | Returned with 401 responses, contains `resource_metadata_url` pointing to MCP server metadata |

Example 401 response header:

```text
WWW-Authenticate: Bearer resource_metadata_url="http://localhost:8000/mcp"
```

### Request headers

[Section titled “Request headers”](#request-headers)

| Header          | Description                                            |
| --------------- | ------------------------------------------------------ |
| `Authorization` | Bearer token for authenticated requests to MCP servers |

Example authenticated request:

```text
Authorization: Bearer <access_token>
```

## Error codes

[Section titled “Error codes”](#error-codes)

The following table contains common HTTP status codes returned by the MCP Authorization Server (this isn’t an exhaustive list):

| Code | Description                                                       |
| ---- | ----------------------------------------------------------------- |
| 400  | Invalid request (malformed parameters or missing required fields) |
| 401  | Unauthorized (authentication failed)                              |
| 403  | Forbidden (Access Policy mismatch)                                |
| 404  | Not found (endpoint or resource doesn’t exist)                    |
| 429  | Too many requests (rate limit exceeded)                           |
| 500  | Internal server error                                             |

## Supported components

[Section titled “Supported components”](#supported-components)

| Component                      | Options                                                                                                                                                                                                                                                                       |
| ------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Identity providers             | OpenID Connect (OIDC) with MFA support; SAML 2.0                                                                                                                                                                                                                              |
| Client Workload identification | [Redirect URI](../../../user-guide/access-policies/client-workloads/identification/redirect-uri.md) and [CIMD Client ID](../../../user-guide/access-policies/client-workloads/identification/cimd-client-id.md); supports Dynamic Client Registration (DCR) and Client ID Metadata Documents (CIMD) |
| Server Workload protocol       | MCP application protocol                                                                                                                                                                                                                                                      |
| Trust Providers                | OIDC ID Token; [SAMLv2 Response](../../../user-guide/access-policies/trust-providers/saml-response-trust-provider.md)                                                                                                                                                                   |

When Enforce SSO is off on the Client Workload, no Trust Provider is required. For guidance on choosing between OIDC and SAML, see [MCP Authorization Server overview](overview.md#choosing-between-oidc-and-saml).

## Dynamic client registration (DCR) support

[Section titled “Dynamic client registration (DCR) support”](#dynamic-client-registration-dcr-support)

Aembit’s MCP Authorization Server supports Dynamic Client Registration (DCR) as defined in RFC 7591, allowing MCP clients to register automatically without pre-configuration.

### Client registration requirements

[Section titled “Client registration requirements”](#client-registration-requirements)

When implementing DCR in your MCP client:

| Requirement                            | Value                           |
| -------------------------------------- | ------------------------------- |
| **Grant Type**                         | `authorization_code` (required) |
| **Response Type**                      | `code` (required)               |
| **Authentication Method**              | `private_key_jwt` (recommended) |
| **Proof Key for Code Exchange (PKCE)** | Required per MCP specification  |

### Registration behavior

[Section titled “Registration behavior”](#registration-behavior)

* **Automatic registration**: Clients can self-register on first connection
* **Standards compliance**: Follows RFC 7591 Dynamic Client Registration specification
* **Unique credentials**: Each client receives a unique `client_id`
* **Redirect URI matching**: Exact match required (port ignored for localhost per MCP spec)

### Implementation notes

[Section titled “Implementation notes”](#implementation-notes)

Ensure your MCP client library supports DCR. The client must:

1. Discover the `registration_endpoint` from the OAuth/OIDC metadata document (exposed at `/.well-known/openid-configuration`)
2. Send a POST request with `redirect_uris`, `grant_types`, and `response_types`
3. Store the returned `client_id` for subsequent requests
4. Include PKCE (`code_challenge` and `code_challenge_method`) in authorization requests

## Client ID Metadata Document (CIMD) support

[Section titled “Client ID Metadata Document (CIMD) support”](#client-id-metadata-document-cimd-support)

Aembit’s MCP Authorization Server also supports OAuth Client ID Metadata Documents (CIMD). Instead of registering through DCR, an MCP client hosts a metadata document at an HTTPS URL, and that URL serves as its `client_id`. An administrator registers the URL on a Client Workload, and Aembit fetches and validates the document at runtime.

The hosted document and its URL must use HTTPS, omit query strings and fragments, return `application/json`, stay within 8 KB, and set a `client_id` that exactly matches the fetch URL. Aembit must reach the URL directly, without redirects or private IP ranges.

For configuration steps and the full requirements, see [CIMD Client ID](../../../user-guide/access-policies/client-workloads/identification/cimd-client-id.md).

## Access token information

[Section titled “Access token information”](#access-token-information)

Aembit’s MCP Authorization Server issues standard OAuth 2.1 JWT access tokens for use with MCP resource servers.

### Token validation for resource servers

[Section titled “Token validation for resource servers”](#token-validation-for-resource-servers)

MCP resource servers validate access tokens using standard OAuth practices:

| Validation Step            | Description                                                                |
| -------------------------- | -------------------------------------------------------------------------- |
| **Signature verification** | Verify using the Authorization Server’s public key from JWKS endpoint      |
| **Issuer (`iss`)**         | Confirm matches your expected Authorization Server (uses `.id.` subdomain) |
| **Audience (`aud`)**       | Confirm matches your resource server URL                                   |
| **Expiration (`exp`)**     | Confirm token hasn’t expired                                               |
| **Scope**                  | Confirm token includes required scopes for the request                     |

### Standard claims

[Section titled “Standard claims”](#standard-claims)

Tokens issued by the MCP Authorization Server include these standard claims:

| Claim   | Description                                              |
| ------- | -------------------------------------------------------- |
| `iss`   | Token issuer - your Aembit tenant’s `.id.` subdomain     |
| `aud`   | Intended audience - your MCP resource server URL         |
| `sub`   | Authenticated user identifier from the identity provider |
| `scope` | Authorized scopes for the request                        |
| `exp`   | Token expiration timestamp                               |
| `iat`   | Token issuance timestamp                                 |

### JWKS endpoint

[Section titled “JWKS endpoint”](#jwks-endpoint)

Token validation keys are available at:

```text
https://[tenant].mcp.[region].aembit.io/.well-known/openid-configuration/jwks
```

Resource servers should cache JWKS responses and refresh periodically per standard OAuth practices. Resource servers should obtain this from the `jwks_uri` field in the discovery document.

## MCP server token configuration

[Section titled “MCP server token configuration”](#mcp-server-token-configuration)

### Configurable token claims

[Section titled “Configurable token claims”](#configurable-token-claims)

MCP servers validate tokens using these configurable claims:

* `aud` (audience)
* `iss` (issuer)
* `sub` (subject)

Token lifetime is configurable on the MCP server side.

### Token algorithm

[Section titled “Token algorithm”](#token-algorithm)

Aembit supports two signing algorithms for access tokens:

* **ES256** - ECDSA with P-256 and SHA-256
* **RSA** - RSA signatures

Algorithm selection

Aembit typically uses ES256 by default and supports RSA-based algorithms, as configured in your Credential Provider.

Configure your MCP server’s JWT verifier to match the algorithm in your Credential Provider settings:

```python
token_verifier = JWTVerifier(
    # ... other configuration
    algorithm="ES256",  # or "RS256" if using RSA
)
```

## Related resources

[Section titled “Related resources”](#related-resources)

* [MCP specification (version 2025-06-18)](https://modelcontextprotocol.io/specification/2025-06-18/basic)
* [MCP Authorization Server overview](overview.md)
* [Set up the MCP Authorization Server](setup-mcp-auth-server.md)
