---
type: reference
title: "MCP Identity Gateway"
description: "Identity federation for MCP clients connecting to MCP servers through Aembit."
resource: https://docs.aembit.io/user-guide/deploy-install/mcp-identity-gateway/
interface: mcp
tags: ["mcp-identity-gateway", "deploy-install"]
timestamp: 2026-09-15T18:18:13-07:00
---

# MCP Identity Gateway

The **Aembit Model Context Protocol (MCP) Identity Gateway** is a data-plane gateway that sits between AI agents and MCP servers. It centralizes how AI agents connect to MCP servers by enforcing Access Policies, performing secure token exchange, and providing visibility into MCP activity.

## What it does

* **Proxies MCP traffic** - AI agents connect to the MCP Identity Gateway as if it were an MCP server. The Gateway connects to downstream MCP servers and relays requests and responses, including both tool invocations and resource access.

* **Enforces identity-aware policies** - The Gateway validates the AI agent’s access token on every MCP request and requests evaluation of the Gateway-to-Server policy from Aembit Cloud to authorize access to the target MCP server. Aembit Cloud evaluates the Client-to-Gateway policy when the AI agent initially connects and obtains an access token. A Trust Provider validates user identity, and OAuth 2.0 Authorization Code Credential Providers manage per-user credentials.

* **Performs secure token exchange** - The Gateway obtains and manages credentials for MCP servers so that AI agents never hold direct credentials for enterprise systems. Supported credential types include OAuth 2.0 tokens (via Authorization Code flow) and API keys. The Gateway obtains credentials per-request and caches them for 60 seconds—it never persists them to disk. For details on the token exchange flow, see [MCP Identity Gateway concepts](concepts-mcp-gateway.md).

* **Governs MCP tool traffic** - When the Gateway-to-Server Access Policy includes a Content Security Provider, the Gateway applies that provider’s decision to each MCP tool message the policy matches. See [Content Security in the MCP Identity Gateway](../../access-policies/mcp-identity-gateway/content-security-mcp-gateway.md).

* **Centralizes MCP routing** - AI agents connect to the Gateway endpoint instead of connecting directly to MCP servers. The Gateway routes requests to the configured MCP server based on policy.

* **Provides auditability** - The Gateway produces structured logs that capture agent identity, user identity, target MCP server, and policy decisions. These logs integrate with [Log Streams](../../administration/log-streams/overview.md) for centralized observability.

## When to use it

Use the MCP Identity Gateway when you need:

* Centralized policy enforcement for all MCP traffic from AI agents, including both tool invocations and resource access
* Auditability for which user and agent accessed which MCP server, tools, and resources
* Guarantees that AI agents never hold reusable credentials to critical systems

The MCP Identity Gateway targets those who need to secure, control, and audit access from AI agents to third-party and custom-built MCP servers. A single Identity Gateway can route traffic to multiple MCP servers while sharing centralized policies across many users, with each user’s access isolated by their own credentials.

## Deployment options

Aembit offers the MCP Identity Gateway in two deployment models:

* **Aembit-managed (recommended)** - Aembit operates the Gateway for your Tenant at `https://<tenantId>.mcpgateway.aembit.io`, handling provisioning, TLS termination, certificate renewal, and runtime operations. Aembit runs the managed service on Kubernetes on your behalf. To request an endpoint, contact your Aembit representative, then see [Set up the MCP Identity Gateway](../../access-policies/mcp-identity-gateway/setup-mcp-gateway.md).
* **Self-hosted (secondary)** - You run the Gateway as a service on a Linux host you provision and maintain, for cases where it must stay in your own infrastructure. See [Self-host the MCP Identity Gateway](self-host-mcp-gateway.md).

## How it works

The following diagram shows the request flow from a user through the MCP Identity Gateway:

![Request flow from a user through the MCP Identity Gateway to an MCP server](https://docs.aembit.io/d2/docs/user-guide/deploy-install/mcp-identity-gateway/index-0.svg)

1. A user sends a prompt to their large language model (LLM) client (AI agent).
2. The AI agent connects to the MCP Identity Gateway and presents a signed JSON Web Token (JWT) that identifies the agent and user.
3. The Gateway validates the token, requests policy evaluation from Aembit Cloud, and proxies the request to the MCP server using credentials that Aembit manages. If token validation or policy evaluation fails, the Gateway rejects the request (fail-closed).
4. The MCP server processes the request and returns a response.
5. The Gateway relays the response back to the AI agent.

The AI agent never receives credentials for the MCP server—the Gateway handles credential exchange transparently.

For architecture details, see [MCP Identity Gateway concepts](concepts-mcp-gateway.md).

## Identity validation

The MCP Identity Gateway validates identity through three distinct processes: initial authentication (one-time setup), per-request token validation, and per-request access policy evaluation.

### Initial authentication (one-time setup)

Before an AI agent can make MCP requests, it must obtain credentials through a one-time authentication flow:

1. **User authentication** - The user authenticates to their identity provider (such as Google, Okta, or Microsoft Entra ID) through single sign-on (SSO).
2. **Access token issuance** - The identity provider issues a signed access token containing the user’s identity claims (`iss`, `sub`, `aud`, `email`).
3. **Aembit access token issuance** - The Aembit Authorization Server validates the access token and issues an Aembit-signed access token (an OAuth 2.0 access token in JWT format).
4. **Token storage** - The AI agent stores the Aembit access token for subsequent MCP requests.

This authentication process happens once during MCP client configuration. The AI agent reuses the stored token for all subsequent requests until it expires.

### Token validation (per request)

On every MCP request, the Gateway validates the token before processing:

1. **JWT format** - The AI agent presents an access token (a signed JWT) with each request.
2. **Signature validation** - The Gateway validates the token signature using Aembit’s signing keys (JWKS).
3. **Audience check** - The token `aud` claim must match the Gateway URL.
4. **Claims extraction** - The Gateway extracts user identity and agent identity from the token.
5. **Trust Provider validation** - The configured Trust Provider validates the token issuer, audience, and key material for your identity provider.

If token validation fails, the Gateway rejects the request immediately.

### Access policy evaluation (per request)

After token validation succeeds, the Gateway requests policy evaluation from Aembit Cloud:

1. **Client-to-Gateway policy** - Validates which MCP client is connecting and which user it represents.
2. **Gateway-to-Server policy** - Authorizes access to the target MCP server. Optional Access Conditions can enforce time-based or geographic restrictions.
3. **Credential retrieval** - The Credential Provider retrieves the user’s OAuth tokens for the target MCP server.

If policy evaluation fails, the Gateway rejects the request and logs the decision.

For details on the security model, see [MCP Identity Gateway concepts](concepts-mcp-gateway.md).

## Security properties

The MCP Identity Gateway provides the following security guarantees:

* **Credential isolation** - AI agents never receive credentials for MCP servers. The Gateway obtains and manages credentials on their behalf.
* **Fail-closed behavior** - If token validation or policy evaluation fails, the Gateway rejects the request. The Gateway denies requests by default unless a policy explicitly allows them.
* **Audit trail** - The Gateway logs MCP traffic with agent identity, user identity, target server, and policy decision so you can see which agents and users accessed which MCP servers and when.
* **Centralized policy** - Aembit policies govern access, not scattered configurations in individual agents.

**What the Gateway doesn’t do:**

* The Gateway doesn’t replace the MCP server’s internal authorization logic.

## Troubleshooting and observability

* [Troubleshoot MCP and AI IAM access](../../troubleshooting/mcp-ai-iam.md) - End-to-end investigation guide for MCP authorization and Gateway failures
* [MCP Authorization Tracing](../../audit-report/mcp-authorization-tracing.md) - Live view of inbound MCP authorization requests at the Gateway
* [Access Authorization Events](../../audit-report/access-authorization-events.md) - Authorization decisions made by Aembit Cloud, including the `access.discovery` event type
* [Workload Events](../../audit-report/workload-events/overview.md) - Request and response traffic that flowed through the Gateway, including MCP-specific fields

## In this section

* [Concepts](concepts-mcp-gateway.md) - Architecture, security model, token handling, access policies, deployment patterns, and operational considerations
* [Content Security](../../access-policies/mcp-identity-gateway/content-security-mcp-gateway.md) - How Content Security inspects MCP traffic in the Gateway request path
* [Client workload identification](../../access-policies/mcp-identity-gateway/client-workload-identification.md) - How the Gateway identifies users in multi-user deployments
* [Setup](../../access-policies/mcp-identity-gateway/setup-mcp-gateway.md) - Configure your Aembit Tenant for the managed Gateway, with validation steps
* [Connect Microsoft Copilot Studio](connect-copilot-studio.md) - Connect Copilot Studio agents to enterprise MCP servers through the Gateway
* [Self-host](self-host-mcp-gateway.md) - Deploy and operate the Gateway on your own Linux host (secondary option)
* [Reference](reference-mcp-gateway.md) - Token formats, proxied MCP methods, session management, and workload events
* [Environment variables](env-vars-mcp-gateway.md) - Operator reference for the environment variables Aembit sets when provisioning a Gateway
