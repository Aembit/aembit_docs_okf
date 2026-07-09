---
type: explanation
title: "MCP Authorization Server"
description: "Secure OAuth 2.1 authorization for Model Context Protocol (MCP) clients and servers using Aembit Access Policies."
resource: https://docs.aembit.io/ai-guide/mcp/authorization-server/
tags: [authorization-server, mcp]
timestamp: 2026-05-19T19:12:05-07:00
---

# MCP Authorization Server

Aembit’s MCP**Model Context Protocol**: A standard protocol for AI agent and server interactions that defines how AI assistants communicate with external tools and data sources.[Learn more(opens in new tab)](https://modelcontextprotocol.io/) Authorization Server secures MCP workloads using OAuth 2.1 authorization flows. It implements the authorization functionality defined in the [MCP specification](https://modelcontextprotocol.io/specification/2025-06-18/basic). AI agents and MCP clients authenticate and receive access tokens governed by Aembit Access Policies**Access Policy**: Access Policies define, enforce, and audit access between Client and Server Workloads by cryptographically verifying workload identity and contextual factors rather than relying on static secrets.[Learn more](../../../get-started/concepts/access-policies.md).

## What it does

[Section titled “What it does”](#what-it-does)

* **Handles OAuth for MCP** - Implements the OAuth 2.1 authorization code flow from the MCP specification so you don’t have to build it yourself
* **Works with existing MCP clients** - Supports Dynamic Client Registration**Dynamic Client Registration**: An OAuth mechanism that allows MCP clients to register with the Authorization Server at runtime without pre-configuration, receiving unique credentials for subsequent authorization requests.[Learn more](concepts-mcp-auth-server.md#client-registration), so tools like Gemini CLI and Claude Desktop can connect without pre-configuration
* **Uses your existing identity provider** - Integrates with OIDC and SAML providers (Okta, Azure AD, Google) for user authentication, or skip user authentication entirely for use cases that don’t require it
* **Adds access control** - Apply Aembit Access Policies**Access Policy**: Access Policies define, enforce, and audit access between Client and Server Workloads by cryptographically verifying workload identity and contextual factors rather than relying on static secrets.[Learn more](../../../get-started/concepts/access-policies.md) to restrict who can access which MCP servers, with optional time and location conditions
* **Runs in Aembit Cloud** - Available in v1.27+ with no additional agents to deploy

## When to use it

[Section titled “When to use it”](#when-to-use-it)

Use Aembit’s MCP Authorization Server when you need to:

* Secure MCP-compliant workloads or AI agents using OAuth 2.1 flows
* Apply fine-grained access control, including geo/time-based conditions and integration with OIDC identity providers
* Enable dynamic client registration and seamless integration with MCP clients like Claude Desktop and Gemini CLI, or use MCP Jam for testing and debugging your authorization flows

## How it works

[Section titled “How it works”](#how-it-works)

Aembit’s MCP Authorization Server processes requests through these steps:

1. **Client registration** - MCP clients register with the MCP Authorization Server using a [redirect URI](../../../user-guide/access-policies/client-workloads/identification/redirect-uri.md) as their identifier, via dynamic client registration
2. **Authorization request** - Clients initiate OAuth 2.1 flows. When **Enforce SSO** is on (the default), users authenticate through their configured identity provider. When Enforce SSO is off, the flow skips user authentication
3. **Policy evaluation** - Aembit evaluates Access Policies, including access conditions. When Enforce SSO is on, Trust Provider attestation also applies
4. **Token issuance** - On successful authorization, the server issues access tokens for use with MCP servers using the OIDC ID Token Credential Provider
5. **Token validation** - MCP servers validate tokens using standard [OIDC](https://openid.net/specs/openid-connect-core-1_0.html)/JWKS**JWKS**: JSON Web Key Set - A set of cryptographic keys published at a well-known endpoint, used to verify the signatures of JSON Web Tokens (JWTs) issued by an authorization server.[Learn more](reference-mcp-auth-server.md) mechanisms. Aembit lets you configure the audience, issuer, subject claims, and token lifetime

For an architecture diagram showing these components, see [MCP Authorization Server architecture](concepts-mcp-auth-server.md#mcp-authorization-server-architecture).

## Authentication support

[Section titled “Authentication support”](#authentication-support)

Aembit’s MCP Authorization Server supports human/user authentication through OIDC and SAML Identity Providers, as well as flows that skip user authentication entirely.

| Authentication type    | Description                                                                     | Supported? |
| ---------------------- | ------------------------------------------------------------------------------- | ---------- |
| Human/user (SSO)       | Users authenticate via OIDC or SAML Identity Providers (Azure AD, Okta, Google) | ✅          |
| No user authentication | OAuth flow completes without user sign-in, for use cases like ChatGPT apps      | ✅          |
| Non-human workload     | Service accounts, AWS IAM roles, Azure Managed Identity                         | ❌          |

The **Enforce SSO** option on your Client Workload controls which flow applies. Enforce SSO is on by default, requiring user authentication. To skip user authentication, turn off Enforce SSO on your Client Workload. See [Set up the MCP Authorization Server](setup-mcp-auth-server.md#create-a-client-workload) for configuration details.

### Choosing between OIDC and SAML

[Section titled “Choosing between OIDC and SAML”](#choosing-between-oidc-and-saml)

When Enforce SSO is on, Aembit’s MCP Authorization Server supports both OIDC and SAML identity providers. Both require a Credential Provider to generate access tokens, but they differ in Trust Provider support:

| Protocol                       | Trust Provider                 | Credential Provider |
| ------------------------------ | ------------------------------ | ------------------- |
| OIDC                           | OIDC ID Token Trust Provider   | Required            |
| SAML                           | SAMLv2 Response Trust Provider | Required            |
| No user auth (Enforce SSO off) | None                           | Required            |

* **OIDC**: Use the [OIDC ID Token Trust Provider](../../../user-guide/access-policies/trust-providers/oidc-id-token-trust-provider.md) in your Access Policy to validate identity tokens. A Credential Provider generates the access token.
* **SAML**: Use the [SAMLv2 Response Trust Provider](../../../user-guide/access-policies/trust-providers/saml-response-trust-provider.md) in your Access Policy to validate SAML responses. A Credential Provider generates the access token.
* **No user auth**: When Enforce SSO is off, no Trust Provider or identity provider is needed. A Credential Provider still generates the access token.

When Enforce SSO is on, both protocols require an identity provider configured in your Aembit tenant under **Administration > Identity Providers**.

If you configure multiple identity providers for your tenant, users select their IdP during the authentication flow.

## Observability and troubleshooting

[Section titled “Observability and troubleshooting”](#observability-and-troubleshooting)

Aembit’s MCP Authorization Server uses the same observability and audit pipeline as the rest of Aembit Cloud. MCP-related activity appears in:

* **[Access Authorization Events](../../../user-guide/audit-report/access-authorization-events.md)** — authorization decisions for MCP client and server workloads, including the `access.discovery` event type and Access Condition failures
* **[Workload Events](../../../user-guide/audit-report/workload-events.md)** — request and response traffic for MCP flows, with MCP-specific user and workload fields
* **[MCP Authorization Tracing](../../../user-guide/audit-report/mcp-authorization-tracing.md)** — live view of inbound MCP authorization requests at the Identity Gateway

For end-to-end investigation that ties these surfaces together, see [Troubleshoot MCP and AI IAM access](../../../user-guide/troubleshooting/mcp-ai-iam.md).

View and export this data using Aembit’s standard tools: [Admin Dashboard](../../../user-guide/administration/admin-dashboard/overview.md) for interactive inspection, or [Log Streams](../../../user-guide/administration/log-streams/overview.md) to export to external systems such as a SIEM.

## In this section

[Section titled “In this section”](#in-this-section)

* [MCP Authorization Server concepts](concepts-mcp-auth-server.md) - URL configuration, token handling, and Access Policy components
* [Set up the MCP Authorization Server](setup-mcp-auth-server.md) - Configure Access Policies and deploy the service
* [MCP Authorization Server reference](reference-mcp-auth-server.md) - Configuration options, endpoints, and error codes
* [Troubleshoot the MCP Authorization Server](troubleshooting-mcp-auth-server.md) - Common errors and solutions
