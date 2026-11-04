---
type: explanation
title: "MCP Identity Gateway concepts"
description: "Architecture, token handling, Access Policies, and deployment patterns for MCP Identity Gateway."
resource: https://docs.aembit.io/user-guide/deploy-install/mcp-identity-gateway/concepts-mcp-gateway/
tags: ["mcp-identity-gateway", "deploy-install"]
timestamp: 2026-11-02T13:04:06-08:00
---

# MCP Identity Gateway concepts

MCP Identity Gateway is a data-plane proxy between AI agents and MCP servers that evaluates every MCP interaction against Aembit Access Policies. For setup instructions, see [Set up MCP Identity Gateway](../../access-policies/mcp-identity-gateway/setup-mcp-gateway.md) (managed) or [Self-host MCP Identity Gateway](self-host-mcp-gateway.md).

## Core actors

The MCP Identity Gateway architecture involves:

* **AI Agent (MCP Client)** - MCP-capable AI agents (such as Claude) that connect to MCP Identity Gateway
* **MCP Identity Gateway** - The data-plane proxy. Aembit operates it as a managed service for your Aembit Tenant at `https://<tenant-id>.mcpgateway.aembit.io` (replace `<tenant-id>` with your Aembit Tenant ID), or you [self-host it](self-host-mcp-gateway.md) on your own Linux host.
* **Aembit Cloud** - Evaluates Access Policies and provides credentials via Credential Providers
* **Identity Provider (IdP)** - Your IdP (Okta, Google, Entra ID) authenticates users
* **MCP servers** - The target services providing tools and resources

## User identity in MCP traffic

Unlike typical workload-to-workload scenarios, MCP traffic involves a human user operating an AI agent. This means every MCP request carries **two identities**:

* **MCP client identity (workload)** - The AI agent application making the request, identified by its OAuth redirect URL (for example, `https://claude.ai/api/mcp/auth_callback`)
* **User identity (human)** - The person using the AI agent, identified by claims from your IdP such as their email address

MCP Identity Gateway uses both identities for access control. The MCP client identity determines *which* AI agents can connect, while the user identity determines *who* can access which MCP servers and enables per-user credential isolation.

In the client-to-Gateway policy, the Credential Provider extracts the user’s identity from their IdP token. You specify this using a dynamic claim expression like `${oidc.identityToken.decode.payload.<user-claim>}`. Replace `<user-claim>` with a claim from your IdP that uniquely identifies users (such as `email`, `sub`, or `preferred_username`). This embeds the user’s identity in the access token, enabling Aembit to:

* Enforce per-user Access Policies
* Store OAuth tokens for each user in isolation
* Provide user-level audit trails showing who accessed which MCP servers

## MCP Identity Gateway architecture

The following diagram shows how traffic flows through MCP Identity Gateway. The flow is identical in both deployment models. Only where MCP Identity Gateway runs differs (inside Aembit’s managed service, or inside your own environment when you self-host).

* Aembit-managed

  ![Traffic flow from user and AI agent through the Aembit-managed MCP Identity Gateway to an MCP server](https://docs.aembit.io/d2/docs/user-guide/deploy-install/mcp-identity-gateway/concepts-mcp-gateway-0.svg)

* Self-hosted

  ![Traffic flow from user and AI agent through a self-hosted MCP Identity Gateway to an MCP server](https://docs.aembit.io/d2/docs/user-guide/deploy-install/mcp-identity-gateway/concepts-mcp-gateway-1.svg)

## Authorization flows

MCP Identity Gateway orchestrates two flows: initial authentication and per-request authorization.

### Initial authentication

The AI agent initiates an OAuth flow with Aembit’s Authorization Server, which redirects the user to their Identity Provider for authentication. After validating the user’s OIDC token against the Client-to-Gateway policy, Aembit issues a long-lived access token. This token embeds the user’s identity and enables per-user Access Policies. This happens once per AI agent.

### Per-request authorization

For each request, MCP Identity Gateway validates the access token locally, then requests an assessment from Aembit Cloud. The Policy Engine evaluates the Gateway-to-Server policy and returns credentials if allowed. MCP Identity Gateway proxies the request using those credentials. It is stateless: every request triggers fresh policy evaluation with per-request credentials.

## Identity-aware Access Policies

MCP Identity Gateway evaluates every MCP interaction against Aembit Access Policies. It requires **two Access Policies** to function:

![Two Access Policies required by the MCP Identity Gateway: client-to-gateway and gateway-to-server](https://docs.aembit.io/d2/docs/user-guide/deploy-install/mcp-identity-gateway/concepts-mcp-gateway-2.svg)

You configure one Client-to-Gateway policy per MCP Identity Gateway and one Gateway-to-Server policy per MCP server—not one policy per user. This model supports many users sharing the same policies while maintaining per-user credential isolation through the Credential Provider.

**Client-to-Gateway policy** - Validates which MCP client is connecting (by redirect URL) and authenticates the user through your IdP via a Trust Provider.

This policy handles blended access: both the AI agent identity and the user identity.

**Gateway-to-Server policy** - Authorizes MCP Identity Gateway to access MCP servers on behalf of authenticated users. The Credential Provider manages access to the MCP server, with optional Access Conditions for time or GeoIP restrictions. Use [MCP User-Based Access Token](../../access-policies/credential-providers/about-mcp-user-based-access-token.md) when the MCP server requires per-user OAuth credentials (most SaaS MCP servers). Use [OAuth 2.0 Authorization Code](../../access-policies/credential-providers/oauth-authorization-code.md) when the server accepts a shared, administrator-authorized token. Use [MCP Enterprise Managed Access Token](../../access-policies/credential-providers/about-mcp-enterprise-managed-access-token.md) when the MCP server and your OIDC Identity Provider both support Enterprise-Managed Authorization, so users never see a consent prompt for the server.

With MCP User-Based Access Token, each user completes an OAuth flow once per MCP server. If unauthorized, MCP Identity Gateway denies the request and logs the event.

### Left-side and right-side auth

The Client-to-Gateway and Gateway-to-Server policies are also called **left-side auth** and **right-side auth**. These terms appear in Aembit documentation and support conversations.

The naming is positional: MCP Identity Gateway sits in the middle of every MCP request. The left side is the inbound connection from MCP clients. The right side is the outbound connection to MCP servers.

* **Left-side auth (Client-to-Gateway)** - Controls who can connect to MCP Identity Gateway: it validates MCP client identity and authenticates users through your IdP. One left-side policy per MCP client and MCP Identity Gateway combination.
* **Right-side auth (Gateway-to-Server)** - Controls where MCP Identity Gateway routes traffic and how it authenticates to each destination. One right-side policy per MCP server, so an MCP Identity Gateway that routes to multiple servers has multiple right-side policies.

## Token exchange

A central design principle: MCP Identity Gateway scopes each credential to the connection that needs it:

* **AI agent → MCP Identity Gateway** - The AI agent holds a token that identifies the AI agent and user. This token is only valid against MCP Identity Gateway.
* **MCP Identity Gateway → MCP server** - MCP Identity Gateway holds a token that is valid against the MCP server. MCP Identity Gateway never exposes this token to the AI agent.

With MCP Enterprise Managed Access Token, Aembit Cloud obtains the server-side token by exchanging the user’s SSO identity. It requests an identity assertion from the corporate identity provider, then an access token from the MCP server’s authorization server. The corporate identity provider must support token exchange, and Okta is the only one Aembit has verified. No user completes a consent flow. See [About the MCP Enterprise Managed Access Token Credential Provider](../../access-policies/credential-providers/about-mcp-enterprise-managed-access-token.md).

This separation allows you to:

* Revoke or change access by updating Aembit policies without reconfiguring AI agents
* Rotate MCP-side credentials without redistributing anything to AI agents
* Prove for audit purposes that AI agents never received direct credentials for sensitive systems

## Content Security inspection

MCP Identity Gateway applies a Content Security Provider when the Gateway-to-Server Access Policy includes one. It applies the provider’s decision to each MCP tool message the policy matches. Inspection runs after token validation and policy evaluation succeed, so it adds content evaluation to the request path without changing how MCP Identity Gateway authenticates and authorizes MCP traffic.

For the inspection sequence, checkpoints, and failure behavior, see [Content Security in MCP Identity Gateway](../../access-policies/mcp-identity-gateway/content-security-mcp-gateway.md).

## Security model

MCP Identity Gateway enforces four security boundaries:

* **Token validation** - MCP Identity Gateway validates incoming tokens against the configured Trust Provider before processing any request. Aembit rejects unauthenticated requests immediately with a `401` response and they are never forwarded to MCP servers.
* **Transport security** - All connections use Transport Layer Security (TLS) 1.2 or higher. MCP Identity Gateway terminates TLS from clients and initiates new TLS connections to MCP servers.
* **Credential isolation** - MCP Identity Gateway holds downstream credentials only in memory and never exposes them to AI agents.
* **Policy enforcement** - MCP Identity Gateway evaluates every request against Aembit Access Policies before proxying it.

## Deployment modes

You can run MCP Identity Gateway in one of two modes. The traffic flow and policy model are identical in both. Only who operates MCP Identity Gateway, and how many Aembit Tenants it serves, differ.

* **Self-hosted mode** - You run MCP Identity Gateway on your own host, where it serves only your Aembit Tenant, so all its runtime state belongs to your Aembit Tenant by definition. See [Self-host MCP Identity Gateway](self-host-mcp-gateway.md).
* **Managed mode** - Aembit runs MCP Identity Gateway for you as a multi-tenant SaaS service, reachable at your per-Aembit Tenant endpoint `https://<tenant-id>.mcpgateway.aembit.io`. See [Set up MCP Identity Gateway](../../access-policies/mcp-identity-gateway/setup-mcp-gateway.md).

## MCP resource support

MCP Identity Gateway proxies MCP resource requests using the same infrastructure that handles tool requests. Resources require no additional configuration. The same Access Policies, authentication flows, and credential isolation that govern tool requests apply automatically to resource requests.

### What are MCP resources?

**Tools** and **resources** are the MCP server capabilities most relevant to MCP Identity Gateway.

* **Tools** are actions that an LLM decides to invoke, such as sending an email or querying a database. The LLM selects which tool to call and provides the required parameters.
* **Resources** are data that an application or user retrieves for context, such as reading a file, fetching a database schema, or loading documentation. The client application (not the LLM) decides which resources to include.

| Aspect            | Tools                                           | Resources                                             |
| ----------------- | ----------------------------------------------- | ----------------------------------------------------- |
| **Purpose**       | Perform actions                                 | Provide context data                                  |
| **Who initiates** | The LLM decides when to invoke                  | The application or user decides what to include       |
| **Input**         | Parameterized (JSON Schema)                     | Single URI                                            |
| **Output**        | Structured results                              | Raw content (text or binary)                          |
| **Example**       | `tools/call` with `{"name": "send_email", ...}` | `resources/read` with `{"uri": "file:///schema.sql"}` |

Think of tools as **actions you can take** and resources as **reference material you can read**.

### How MCP Identity Gateway handles resources

MCP Identity Gateway proxies resource discovery and retrieval using the same request flow as tools. Clients can list available resources across all connected MCP servers and read individual resources by URI. The existing two Access Policy model (Client-to-Gateway and Gateway-to-Server) applies identically. You do not need to create separate Access Policies for resources.

What a client can discover reflects the union of what the assigned MCP servers support. It is not a fixed list and not what the client declared.

For the specific MCP methods that MCP Identity Gateway proxies, see the [proxied MCP methods](reference-mcp-gateway.md#proxied-mcp-methods) reference.

For more on the distinction between MCP servers and MCP apps, see [MCP servers and MCP apps](../about-mcp-servers-and-apps.md).

## Next steps

### Get started

* [Set up MCP Identity Gateway](../../access-policies/mcp-identity-gateway/setup-mcp-gateway.md) - Deploy and configure MCP Identity Gateway

### Learn more

* [Content Security in MCP Identity Gateway](../../access-policies/mcp-identity-gateway/content-security-mcp-gateway.md) - How Content Security inspects MCP traffic in MCP Identity Gateway’s request path
* [Client Workload identification](../../access-policies/mcp-identity-gateway/client-workload-identification.md) - How MCP Identity Gateway identifies users in multi-user deployments
* [MCP servers and MCP apps](../about-mcp-servers-and-apps.md) - How Aembit handles different types of MCP services
* [Environment variables](env-vars-mcp-gateway.md) - Configuration reference
* [Reference](reference-mcp-gateway.md) - Networking, logging, and operational details
* [MCP User-Based Access Tokens](../../access-policies/credential-providers/about-mcp-user-based-access-token.md) - When to use this Credential Provider and how it works
* [Set up MCP User-Based Access Tokens](../../access-policies/credential-providers/mcp-user-based-access-token.md) - Step-by-step configuration guide
* [MCP Enterprise Managed Access Tokens](../../access-policies/credential-providers/about-mcp-enterprise-managed-access-token.md) - When to use this Credential Provider and how the token exchange works
* [Set up MCP Enterprise Managed Access Tokens](../../access-policies/credential-providers/mcp-enterprise-managed-access-token.md) - Step-by-step configuration guide
* [MCP Authorization Server concepts](../mcp-authorization-server/concepts-mcp-auth-server.md) - How left-side and right-side auth apply in the Authorization Server context
