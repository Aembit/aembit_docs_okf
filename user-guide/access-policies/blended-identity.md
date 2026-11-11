---
type: explanation
title: "Understanding Blended Identities"
description: "How Aembit combines user identity and AI agent workload identity into unified access decisions"
resource: https://docs.aembit.io/user-guide/access-policies/blended-identity/
tags: ["access-policy"]
timestamp: 2026-11-09T14:11:42-08:00
---

# Understanding Blended Identities

Traditional IAM answers one of two questions: “who is this user?” (user identity) or “what is this workload?” (workload identity). AI agents require both answers at the same time.

When an employee uses Claude Desktop to query Jira, two identities are in play:

* **User identity** - the human, authenticated through your Identity Provider (Okta, Entra ID, Google). This determines *who* is responsible for the AI agent’s actions.
* **Workload identity** - the AI agent application itself, identified by properties like its OAuth redirect URL. This determines *which* AI agent is making the request.

Blended identity means Aembit evaluates both identities in a single Access Policy. That policy asks: is this user, using this AI agent, authorized to reach this resource right now?

## Why blended identity matters

Without blended identity, organizations face a binary choice:

* **User-only identity** (traditional IAM) - You know who the user is but treat all AI agents identically. Every AI assistant gets the same access regardless of its trust level or capabilities.
* **Workload-only identity** (traditional workload IAM) - You know which AI agent is connecting but cannot distinguish between users behind it. All users of Claude Desktop get identical access with no per-user scoping.

Blended identity eliminates this trade-off. Access Policies can express rules like:

* “Engineers can use Claude Desktop to access Jira, but only the security team can use it to access the vulnerability scanner”
* “This user can access Confluence through Claude Desktop but not through an untrusted third-party AI agent”
* “Revoke this specific user’s AI agent access without affecting other users or rotating shared credentials”

## How Aembit implements blended identity

Aembit implements blended identity through its Access Policy model, where policies evaluate user attributes and workload attributes simultaneously.

### MCP Authorization Server

The [MCP Authorization Server](../deploy-install/mcp-authorization-server/overview.md) implements blended identity in a single Access Policy:

* The **Client Workload** identifies the AI agent by its OAuth redirect URL
* The **Trust Provider** validates the user’s identity through your IdP (OIDC or SAML claims)
* The **Credential Provider** issues a short-lived token scoped to both the user and the AI agent

The user authenticates through your IdP, the redirect URL identifies the AI agent, and the policy evaluates both before issuing a token.

### MCP Identity Gateway

[MCP Identity Gateway](../deploy-install/mcp-identity-gateway/overview.md) extends blended identity across two Access Policies:

* **Client-to-Gateway policy** - Validates the AI agent’s identity (redirect URL) *and* the user’s identity (IdP claims) together. The Credential Provider embeds the user’s identity into the access token using dynamic claim expressions like `${oidc.identityToken.decode.payload.email}`.
* **Gateway-to-Server policy** - Uses MCP Identity Gateway’s workload identity plus the embedded user context to obtain credentials for each downstream MCP server.

With an MCP User-Based or Enterprise Managed Access Token Credential Provider, this architecture enables per-user credential isolation. Each user’s requests use their own downstream credentials based on their identity, even though all users connect through the same MCP Identity Gateway.

### Per-user credential isolation

With an MCP User-Based or Enterprise Managed Access Token Credential Provider, Aembit scopes credentials to individual users, not shared across an AI agent type. When MCP Identity Gateway connects to a downstream MCP server on behalf of a user with an MCP User-Based Access Token Credential Provider:

* Aembit stores OAuth tokens per user, not per AI agent
* Each user completes their own authorization flow for each MCP server
* Revoking one user’s access does not affect others

This means User A’s Claude Desktop session accesses Jira with User A’s permissions, while User B’s session uses User B’s permissions. Both users connect through the same MCP Identity Gateway with the same policies.

## Blended identity and audit

Every access event logged by Aembit includes both identity dimensions:

* **Who** - The authenticated user (for example, `user@example.com`)
* **What AI agent** - The AI agent that made the request (for example, Claude Desktop via `https://claude.ai/...`)
* **Which resource** - The MCP server and operation accessed
* **Policy outcome** - Which Access Policy evaluated and whether Aembit granted or denied access

This dual attribution satisfies compliance requirements (SOC 2, HIPAA, PCI) that need clear accountability for every action. Workload-only and user-only identity models cannot provide that accountability for AI agent access.

## Next steps

* [Securing AI agent access](../../get-started/use-cases/ai-agents.md) - How blended identity fits into the broader AI agent security use case
* [MCP Authorization Server](../deploy-install/mcp-authorization-server/overview.md) - Blended identity in the authorization-only model
* [MCP Identity Gateway concepts](../deploy-install/mcp-identity-gateway/concepts-mcp-gateway.md) - Blended identity in the full proxy model, including its two-Access-Policy architecture
