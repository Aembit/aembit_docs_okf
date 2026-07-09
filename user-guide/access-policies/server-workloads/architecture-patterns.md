---
type: how-to
title: "Server Workload architecture patterns"
description: "Understanding how different authentication methods work with Aembit Server Workloads"
resource: https://docs.aembit.io/user-guide/access-policies/server-workloads/architecture-patterns/
interface: web-ui
tags: [server-workload, access-policy]
timestamp: 2026-04-13T13:05:24-07:00
type_inferred: true
---

# Server Workload architecture patterns

This page explains how Aembit handles different authentication methods when connecting Client Workloads**Client Workload**: Client Workloads represent software applications, scripts, or automated processes that initiate access requests to Server Workloads, operating autonomously without direct user interaction.[Learn more](../../../get-started/concepts/client-workloads.md) to Server Workloads**Server Workload**: Server Workloads represent target services, APIs, databases, or applications that receive and respond to access requests from Client Workloads.[Learn more](../../../get-started/concepts/server-workloads.md).

Understanding these patterns helps you choose the right configuration for your integration and troubleshoot issues.

## How server workloads work

[Section titled “How server workloads work”](#how-server-workloads-work)

All Server Workload integrations follow the same basic flow, regardless of authentication method:

![Generic server workload access flow](https://docs.aembit.io/d2/docs/user-guide/access-policies/server-workloads/architecture-patterns-0.svg)

**Data flow**

1. **Access Request** - Client Workload initiates a request to access the target service (Server Workload)
2. **Policy Lookup** - Aembit Edge**Aembit Edge**: Aembit Edge represents components deployed within your operational environments that enforce Access Policies by intercepting traffic, verifying identities, and injecting credentials just-in-time.[Learn more](../../../get-started/concepts/aembit-edge.md) intercepts the request and queries Aembit Cloud**Aembit Cloud**: Aembit Cloud serves as both the central control plane and management plane, making authorization decisions, evaluating policies, coordinating credential issuance, and providing administrative interfaces for configuration.[Learn more](../../../get-started/concepts/aembit-cloud.md) for the Access Policy**Access Policy**: Access Policies define, enforce, and audit access between Client and Server Workloads by cryptographically verifying workload identity and contextual factors rather than relying on static secrets.[Learn more](../../../get-started/concepts/access-policies.md) and Credential Provider**Credential Provider**: Credential Providers obtain the specific access credentials—such as API keys, OAuth tokens, or temporary cloud credentials—that Client Workloads need to authenticate to Server Workloads.[Learn more](../../../get-started/concepts/credential-providers.md) configuration
3. **Credentials** - Aembit Cloud returns the appropriate credentials based on the Credential Provider configuration
4. **Authenticated Request** - Aembit Edge injects credentials into the request and forwards it to the Server Workload
5. **Response** - The Server Workload processes the authenticated request and returns a response
6. **Response Passthrough** - Aembit Edge forwards the response back to the Client Workload transparently

**Network requirements**

* **Outbound HTTPS (port 443)** from your environment to:
  * Target Server Workload (varies by service)
* **No inbound ports** required for Aembit integration
* **DNS resolution** must work for target service domains

**Component placement**

* **Aembit Edge (Agent Proxy)**: Runs on the same server as your Client Workload, or as a sidecar container in Kubernetes
* **Client Workload**: Runs in your environment (on-premises, cloud VM, container, serverless function)
* **Aembit Cloud**: Hosted service, no infrastructure required
* **Server Workload**: Target service (cloud, on-premises, or third-party SaaS)

## Authentication method variations

[Section titled “Authentication method variations”](#authentication-method-variations)

While the basic flow remains the same, different authentication methods inject credentials into requests differently.

### OAuth flow

[Section titled “OAuth flow”](#oauth-flow)

**Applies to** - [Entra ID](guides/entra-id.md), Salesforce, GitHub (OAuth mode), Okta (OAuth mode)

OAuth-based Server Workloads use the OAuth 2.0 protocol to obtain access tokens. Aembit intercepts OAuth token requests and replaces static client secrets with dynamically generated JWT-SVID**JWT-SVID**: A SPIFFE Verifiable Identity Document in JWT format. JWT-SVIDs are cryptographically signed, short-lived tokens that prove workload identity and enable secure authentication without static credentials.[Learn more](../credential-providers/about-spiffe-jwt-svid.md) credentials.

![OAuth flow for Entra ID, Salesforce, and GitHub](https://docs.aembit.io/d2/docs/user-guide/access-policies/server-workloads/architecture-patterns-1.svg)

**Flow details** -

* **Credential type**: JWT-SVID (JSON Web Token - Secure Verifiable Identity Document) or `client_assertion`

* **Injection point**: OAuth token request body - replaces `client_secret` parameter with dynamic JWT-SVID

* **Data flow**:

  1. Client requests OAuth access token using placeholder credential (for example, `'placeholder-client-secret'`)
  2. Aembit intercepts the token request and removes the placeholder
  3. Aembit generates a short-lived JWT-SVID signed with cryptographic material
  4. Agent Proxy injects the JWT-SVID as `client_assertion` in the token request
  5. OAuth provider validates the JWT-SVID signature
  6. OAuth provider returns access token to the client
  7. Client uses the access token to authenticate API calls to protected resources

**Special considerations** -

* **PKCE support**: Some OAuth providers require Proof Key for Code Exchange (PKCE). Aembit supports PKCE when configured in the Credential Provider.
* **Token refresh**: OAuth SDKs automatically handle token refresh when access tokens expire. Aembit generates a new JWT-SVID for each token refresh request.
* **Scope selection**: The scopes configured in the Server Workload determine which API permissions the access token grants. See individual guide for scope selection guidance.
* **Token lifetime**: JWT-SVIDs are valid for 5 minutes by default. Access tokens from OAuth providers typically last 1 hour but vary by provider.

**Credential lifecycle** -

OAuth credentials have two lifetimes to consider:

* **JWT-SVID lifetime**: 5 minutes (Aembit-generated, used only for token requests)
* **Access token lifetime**: 1 hour typical (provider-issued, used for API calls)

When an access token expires, the OAuth SDK automatically requests a new token, triggering Aembit to generate a fresh JWT-SVID.

### API key flow

[Section titled “API key flow”](#api-key-flow)

**Applies to** - Okta, Claude, OpenAI, GitHub (API Key mode), Stripe, Box

API Key-based Server Workloads inject a static API key into HTTP headers. Aembit retrieves the key from a Credential Provider and injects it transparently.

![API key flow for Okta, Claude, and OpenAI](https://docs.aembit.io/d2/docs/user-guide/access-policies/server-workloads/architecture-patterns-2.svg)

**Flow details** -

* **Credential type**: API key string

* **Injection point**: HTTP `Authorization` header or custom header (for example, `X-API-Key`)

* **Data flow**:

  1. Client makes an API request (no API key in code)
  2. Aembit intercepts the request
  3. Credential Provider retrieves the API key (from secure storage or vault)
  4. Agent Proxy injects the key into the appropriate HTTP header
  5. API service validates the key and processes the request
  6. API service returns response to the client

**Special considerations** -

* **Header format variations**: Different services use different header formats:

  * **Bearer token**: `Authorization: Bearer sk-abc123` (OpenAI, Anthropic)
  * **Single Sign-On Web Services (SSWS) format**: `Authorization: SSWS 00abc123` (Okta)
  * **Custom header**: `X-API-Key: abc123` (some APIs)

* **API key rotation**: When rotating API keys in the service, update the Credential Provider in Aembit. No application code changes required.

* **Rate limiting**: Some services rate-limit by API key. Monitor usage to avoid hitting limits.

* **Key lifetime**: API keys are typically long-lived (months to years). Rotate per security best practices.

**Credential lifecycle** -

Unlike OAuth, API keys are static and long-lived:

* **Storage**: Stored securely in Aembit Credential Provider
* **Rotation**: Manual - update the key in both the service and Aembit Credential Provider
* **Expiration**: Varies by service (some never expire, others expire after 1-2 years)

### Database credential injection

[Section titled “Database credential injection”](#database-credential-injection)

**Applies to** - MySQL, PostgreSQL, Redis, Snowflake, Google BigQuery

Database workloads inject dynamic credentials into database connection strings or authentication commands.

![Database credential injection for MySQL, Postgres, and Redis](https://docs.aembit.io/d2/docs/user-guide/access-policies/server-workloads/architecture-patterns-3.svg)

**Flow details** -

* **Credential type**: Username/password pair or connection string

* **Injection point**: Database connection parameters (replaces username and password fields)

* **Data flow**:

  1. Client opens database connection using placeholder credentials
  2. Aembit intercepts the connection request
  3. Credential Provider generates or retrieves dynamic database credentials
  4. Agent Proxy modifies connection parameters with real credentials
  5. Database validates credentials and establishes connection
  6. Connection is ready for queries

**Special considerations** -

* **Connection pooling**: Aembit works with connection pooling. When the pool creates new connections, Aembit injects credentials.

* **TLS/SSL requirements**: Many databases require TLS encryption. Configure TLS Decrypt in Aembit to intercept encrypted database connections.

* **Credential lifetime vs. connection lifetime**: Database credentials may outlive individual connections. Aembit handles credential rotation without disrupting active connections.

* **Protocol-specific handling**: Different database protocols require different credential injection methods:

  * **MySQL/Postgres**: Username/password in connection parameters
  * **Redis**: Authentication (AUTH) command interception

**Credential lifecycle** -

Database credentials can be static or dynamic:

* **Static credentials**: Stored in Credential Provider, manually rotated
* **Dynamic credentials** (for example, AWS RDS IAM auth): Generated per connection, expire after 15 minutes (typical)

### Cloud provider signatures

[Section titled “Cloud provider signatures”](#cloud-provider-signatures)

**Applies to** - AWS (SigV4), Google Cloud Platform, Azure

Cloud provider workloads use cryptographic request signatures instead of traditional credentials.

**Flow details** -

* **Credential type**: Temporary credentials (access key, secret key, session token)

* **Injection point**: Request signature calculation (replaces AWS Access Key ID and Secret Access Key)

* **Data flow**:

  1. Client makes cloud API request
  2. Aembit intercepts the request
  3. Credential Provider assumes an IAM role or generates temporary credentials
  4. Agent Proxy signs the request using temporary credentials (SigV4 signature)
  5. Cloud provider validates the signature
  6. Cloud provider returns API response

**Special considerations** -

* **IAM role trust relationships**: The IAM role must trust Aembit’s identity provider
* **Session duration limits**: AWS temporary credentials expire after 15 minutes to 12 hours (configurable)
* **Multi-region considerations**: Signatures are region-specific. Configure Credential Provider for the correct region.
* **Service-specific signing**: Different AWS services may require different signing algorithms

**Credential lifecycle** -

* **Temporary credential lifetime**: 15 minutes to 12 hours (AWS default: 1 hour)
* **Automatic refresh**: Aembit automatically obtains fresh credentials before expiration
* **No manual rotation**: Credentials are ephemeral and rotated automatically

### Token-based authentication

[Section titled “Token-based authentication”](#token-based-authentication)

**Applies to** - HashiCorp Vault, Kubernetes Service Accounts

Token-based workloads use bearer tokens for authentication.

**Flow details** -

* **Credential type**: Bearer token (for example, Vault token, Kubernetes service account token)

* **Injection point**: HTTP `Authorization: Bearer <token>` header or X-Vault-Token header

* **Data flow**:

  1. Client makes API request
  2. Aembit intercepts the request
  3. Credential Provider retrieves or generates a token
  4. Agent Proxy injects the token into the request header
  5. Service validates the token and processes the request

**Special considerations** -

* **Token TTL management**: Tokens have time-to-live (TTL) limits. Aembit handles token renewal automatically.
* **Policy-based access control**: Policies in the target service define token permissions (for example, Vault policies)
* **Token renewal**: Some services (like Vault) support token renewal. Aembit can renew tokens before expiration.
* **Bidirectional dependencies**: Services like Vault may require OIDC configuration in both Vault and Aembit. See service-specific guides for setup order.

**Credential lifecycle** -

* **Token lifetime**: Varies by service (Vault default: 32 days, configurable)
* **Renewal**: Automatic before expiration (when supported)
* **Revocation**: You can revoke tokens centrally in the target service

## Choosing the right pattern

[Section titled “Choosing the right pattern”](#choosing-the-right-pattern)

When configuring a new Server Workload, identify which authentication method the target service uses:

| If the service uses…                                   | Use this pattern                                                | Example services                           |
| ------------------------------------------------------ | --------------------------------------------------------------- | ------------------------------------------ |
| **OAuth 2.0** (client credentials, authorization code) | [OAuth Flow](#oauth-flow)                                       | Entra ID, Salesforce, GitHub (OAuth Apps)  |
| **API Keys** in headers                                | [API Key Flow](#api-key-flow)                                   | Okta, Claude, OpenAI, Stripe               |
| **Database credentials** (username/password)           | [Database Credential Injection](#database-credential-injection) | MySQL, Postgres, Redis, Snowflake          |
| **AWS signatures** (SigV4) or GCP/Azure equivalents    | [Cloud Provider Signatures](#cloud-provider-signatures)         | AWS services, GCP services, Azure services |
| **Bearer tokens** or service-specific tokens           | [Token-Based Authentication](#token-based-authentication)       | HashiCorp Vault, Kubernetes                |

See individual [Server Workload guides](guides/overview.md) for detailed configuration steps for each service.

## Related resources

[Section titled “Related resources”](#related-resources)

* **[Developer Integration Guide](developer-integration.md)** - SDK code examples and testing patterns
* **[Troubleshooting Guide](troubleshooting.md)** - Common issues and solutions
* **[Server Workload Guides](guides/overview.md)** - Service-specific configuration guides
* **[Understanding Server Workloads](../../../get-started/concepts/server-workloads.md)** - Conceptual overview
