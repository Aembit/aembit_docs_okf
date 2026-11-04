---
type: explanation
title: "Client Workload identification in MCP Identity Gateway"
description: "How MCP Identity Gateway identifies which user makes each request when multiple users share it."
resource: https://docs.aembit.io/user-guide/access-policies/mcp-identity-gateway/client-workload-identification/
tags: ["mcp-identity-gateway", "access-policy"]
timestamp: 2026-11-02T13:04:06-08:00
---

# Client Workload identification in MCP Identity Gateway

Every MCP request through MCP Identity Gateway carries a blended identity: the AI agent’s workload identity *and* the human user’s identity. When multiple users share one MCP Identity Gateway, it needs to determine which user is behind each request. MCP Identity Gateway resolves the user side of that blended identity, and that identification flows through Access Policies, credential isolation, and audit events.

## How identification works

MCP Identity Gateway identifies users through the OAuth identity embedded in their access token. When a user first connects through an AI agent, the following happens:

1. The user authenticates with their Identity Provider (IdP) through single sign-on (SSO)
2. The Aembit Authorization Server validates the IdP token and issues an Aembit access token
3. The Aembit access token embeds the user’s identity claims (such as email, subject, or preferred username)
4. The AI agent presents this token with every subsequent MCP request

MCP Identity Gateway extracts identity claims from the token on each request. These claims are the foundation for all downstream identification: policy evaluation, credential isolation, and audit logging.

## What MCP Identity Gateway uses for identification

MCP Identity Gateway relies on claims from your IdP token to identify users. You configure which claims to use when you set up the Client-to-Gateway Access Policy.

Common identification claims:

| Claim                | Example value      | When to use                                                                              |
| -------------------- | ------------------ | ---------------------------------------------------------------------------------------- |
| `email`              | `jane@example.com` | Most common. Works when all users have unique email addresses in your IdP.               |
| `sub`                | `auth0\|abc123`    | Use when email is not guaranteed unique or when you need a stable, immutable identifier. |
| `preferred_username` | `jdoe`             | Use when your IdP provides a human-readable username that is unique across users.        |

You specify the claim in the Credential Provider dynamic claim expression:

```text
${oidc.identityToken.decode.payload.<user-claim>}
```

Replace `<user-claim>` with the claim from your IdP that uniquely identifies users.

For details on configuring identity claims, see [User identity in MCP traffic](../../deploy-install/mcp-identity-gateway/concepts-mcp-gateway.md#user-identity-in-mcp-traffic).

## What identification impacts

User identification affects three areas of MCP Identity Gateway:

### Access Policies

The Client-to-Gateway policy uses the user’s identity to determine whether to allow the request. The Trust Provider validates that a trusted IdP issued the token and that the user’s claims meet the policy requirements.

The Gateway-to-Server policy authorizes MCP Identity Gateway to access MCP servers on behalf of the authenticated user. The user’s identity determines which credentials the Credential Provider retrieves.

### Credential isolation

Each user gets their own set of credentials for each MCP server. When User `A` connects to an MCP server through MCP Identity Gateway, Aembit stores User `A`’s credentials in isolation from User `B`’s credentials.

This isolation means:

* Users cannot access each other’s MCP server sessions
* Revoking one user’s access does not affect other users
* Audit trails show per-user credential usage

### Audit and workload events

Workload events in Aembit Cloud capture the user identity associated with each request. This enables you to answer questions like:

* Which user accessed which MCP server and when?
* Which tools did a specific user invoke?
* Are there unusual access patterns for a particular user?

## Multi-user shared MCP Identity Gateway

The most common deployment pattern is a single MCP Identity Gateway shared by multiple users. In this model:

* All users connect to the same MCP Identity Gateway URL
* Each user authenticates with their own IdP credentials
* MCP Identity Gateway identifies each user by the claims in their access token
* Aembit evaluates Access Policies and credentials per user, not per MCP Identity Gateway

This works because MCP Identity Gateway is stateless for authentication purposes. It does not maintain user sessions in the traditional sense. Instead, it extracts the user’s identity from the access token on every request and evaluates policies accordingly.

## See also

* [Blended identity](../blended-identity.md) - How Aembit combines user and workload identity in access decisions
* [MCP Identity Gateway concepts](../../deploy-install/mcp-identity-gateway/concepts-mcp-gateway.md) - Architecture and security model
* [Set up MCP Identity Gateway](setup-mcp-gateway.md) - Deploy and configure MCP Identity Gateway
* [About MCP User-Based Access Tokens](../credential-providers/about-mcp-user-based-access-token.md) - Per-user credential management
