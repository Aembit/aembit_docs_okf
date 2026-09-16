---
type: explanation
title: "Understanding Blended Identities"
description: "How Aembit combines user identity and AI agent workload identity into unified access decisions"
resource: https://docs.aembit.io/user-guide/access-policies/blended-identity/
tags: ["access-policy"]
timestamp: 2026-09-15T18:18:13-07:00
---

# Understanding Blended Identities

Traditional IAM answers one of two questions: “who is this user?” (user identity) or “what is this workload?” (workload identity). AI agents require both answers at the same time.

When an employee uses Claude Desktop to query Jira, two identities are in play:

* **User identity** - the human, authenticated through your Identity Provider (Okta, Entra ID, Google). This determines *who* is responsible for the agent’s actions.
* **Workload identity** - the AI agent application itself, identified by properties like its OAuth redirect URL. This determines *which* agent is making the request.

Blended identity means Aembit evaluates both identities in a single Access Policy. That policy asks: is this user, using this agent, authorized to reach this resource right now?

## Why blended identity matters

Without blended identity, organizations face a binary choice:

* **User-only identity** (traditional IAM) - You know who the user is but treat all agents identically. Every AI assistant gets the same access regardless of its trust level or capabilities.
* **Workload-only identity** (traditional workload IAM) - You know which agent is connecting but can’t distinguish between users behind it. All users of Claude Desktop get identical access with no per-user scoping.

Blended identity eliminates this trade-off. Access policies can express rules like:

* “Engineers can use Claude Desktop to access Jira, but only the security team can use it to access the vulnerability scanner”
* “This user can access Confluence through Claude Desktop but not through an untrusted third-party agent”
* “Revoke this specific user’s AI agent access without affecting other users or rotating shared credentials”

## How Aembit implements blended identity

Aembit implements blended identity through its Access Policy model, where policies evaluate user attributes and workload attributes simultaneously.

### MCP Authorization Server

The [MCP Authorization Server](../deploy-install/mcp-authorization-server/overview.md) implements blended identity in a single access policy:

* The **Client Workload** identifies the AI agent by its OAuth redirect URL
* The **Trust Provider** validates the user’s identity through your IdP (OIDC or SAML claims)
* The **Credential Provider** issues a short-lived token scoped to both the user and the agent

The user authenticates through your IdP, the redirect URL identifies the agent, and the policy evaluates both before issuing a token.

### MCP Identity Gateway

The [MCP Identity Gateway](../deploy-install/mcp-identity-gateway/overview.md) extends blended identity across two policy hops:

* **Client-to-Gateway policy** - Validates the AI agent’s identity (redirect URL) *and* the user’s identity (IdP claims) together. The Credential Provider embeds the user’s identity into the access token using dynamic claim expressions like `${oidc.identityToken.decode.payload.email}`.
* **Gateway-to-Server policy** - Uses the Gateway’s workload identity plus the embedded user context to obtain per-user credentials for each downstream MCP server.

This architecture enables per-user credential isolation: each user’s AI agent receives different downstream credentials based on their identity, even though all users connect through the same Gateway.

### Per-user credential isolation

A key benefit of blended identity is that Aembit scopes credentials to individual users, not shared across an agent type. When the MCP Identity Gateway connects to a downstream MCP server on behalf of a user:

* The Gateway stores OAuth tokens per user, not per agent
* Each user completes their own authorization flow for each MCP server
* Revoking one user’s access doesn’t affect others

This means User A’s Claude Desktop session accesses Jira with User A’s permissions, while User B’s session uses User B’s permissions, even though both connect through the same Gateway with the same policies.

## Blended identity and audit

Every access event logged by Aembit includes both identity dimensions:

* **Who** - The authenticated user (for example, `user@example.com`)
* **What agent** - The AI agent that made the request (for example, Claude Desktop via `https://claude.ai/...`)
* **Which resource** - The MCP server and operation accessed
* **Policy outcome** - Which Access Policy evaluated and whether Aembit granted or denied access

This dual attribution satisfies compliance requirements (SOC 2, HIPAA, PCI) that need clear accountability for every action. Workload-only and user-only identity models can’t provide that accountability for AI agent access.

## Next steps

* [Securing AI agent access](../../get-started/use-cases/ai-agents.md) - How blended identity fits into the broader AI agent security use case
* [MCP Authorization Server](../deploy-install/mcp-authorization-server/overview.md) - Blended identity in the authorization-only model
* [MCP Identity Gateway concepts](../deploy-install/mcp-identity-gateway/concepts-mcp-gateway.md) - Blended identity in the full proxy model, including per-hop policy architecture
