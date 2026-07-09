---
type: explanation
title: "Client workload identification in MCP Identity Gateway"
description: "How the MCP Identity Gateway identifies which user is making requests in multi-user shared Gateway scenarios."
resource: https://docs.aembit.io/ai-guide/mcp/identity-gateway/client-workload-identification/
tags: [identity-gateway, mcp]
timestamp: 2026-06-18T13:15:52-04:00
---

# Client workload identification in MCP Identity Gateway

Every MCP request through Aembit’s Identity Gateway carries a blended identity**Blended Identity**: An access model that combines a human user's identity (authenticated through an Identity Provider) with an AI agent's workload identity into a single access decision, enabling policies that evaluate both "who is this user" and "which agent are they using" simultaneously.[Learn more](../../blended-identity.md): the AI agent’s workload identity *and* the human user’s identity. When multiple users share a single MCP Identity Gateway, the Identity Gateway needs to determine which user is behind each request. This page explains how the Identity Gateway resolves the user side of that blended identity and how that identification flows through Access Policies, credential isolation, and audit events.

## How identification works

[Section titled “How identification works”](#how-identification-works)

The MCP Identity Gateway identifies users through the OAuth identity embedded in their access token. When a user first connects through an AI agent, the following happens:

1. The user authenticates with their Identity Provider (IdP) through single sign-on (SSO)
2. The Aembit Authorization Server validates the IdP token and issues an Aembit access token
3. The Aembit access token embeds the user’s identity claims (such as email, subject, or preferred username)
4. The AI agent presents this token with every subsequent MCP request

The Identity Gateway extracts identity claims from the token on each request. These claims are the foundation for all downstream identification: policy evaluation, credential isolation, and audit logging.

## What the Identity Gateway uses for identification

[Section titled “What the Identity Gateway uses for identification”](#what-the-identity-gateway-uses-for-identification)

The Identity Gateway relies on claims from your IdP token to identify users. You configure which claims to use when you set up the Client-to-Gateway Access Policy**Access Policy**: Access Policies define, enforce, and audit access between Client and Server Workloads by cryptographically verifying workload identity and contextual factors rather than relying on static secrets.[Learn more](../../../get-started/concepts/access-policies.md).

Common identification claims:

| Claim                | Example value      | When to use                                                                             |
| -------------------- | ------------------ | --------------------------------------------------------------------------------------- |
| `email`              | `jane@example.com` | Most common. Works when all users have unique email addresses in your IdP.              |
| `sub`                | `auth0\|abc123`    | Use when email isn’t guaranteed unique or when you need a stable, immutable identifier. |
| `preferred_username` | `jdoe`             | Use when your IdP provides a human-readable username that’s unique across users.        |

You specify the claim in the Credential Provider**Credential Provider**: Credential Providers obtain the specific access credentials—such as API keys, OAuth tokens, or temporary cloud credentials—that Client Workloads need to authenticate to Server Workloads.[Learn more](../../../get-started/concepts/credential-providers.md) dynamic claim expression:

```plaintext
${oidc.identityToken.decode.payload.<user_claim>}
```

Replace `<user_claim>` with the claim from your IdP that uniquely identifies users.

For details on configuring identity claims, see [User identity in MCP traffic](concepts-mcp-gateway.md#user-identity-in-mcp-traffic).

## What identification impacts

[Section titled “What identification impacts”](#what-identification-impacts)

User identification affects three areas of the MCP Identity Gateway:

### Access policies

[Section titled “Access policies”](#access-policies)

The Client-to-Gateway policy uses the user’s identity to determine whether to allow the request. The Trust Provider**Trust Provider**: Trust Providers validate Client Workload identities through workload attestation, verifying identity claims from the workload's runtime environment rather than relying on pre-shared secrets.[Learn more](../../../get-started/concepts/trust-providers.md) validates that the token was issued by a trusted IdP and that the user’s claims meet the policy requirements.

The Identity Gateway-to-Server policy authorizes the Identity Gateway to access MCP servers on behalf of the authenticated user. The user’s identity determines which credentials the Credential Provider**Credential Provider**: Credential Providers obtain the specific access credentials—such as API keys, OAuth tokens, or temporary cloud credentials—that Client Workloads need to authenticate to Server Workloads.[Learn more](../../../get-started/concepts/credential-providers.md) retrieves.

### Credential isolation

[Section titled “Credential isolation”](#credential-isolation)

Each user gets their own set of credentials for each MCP server. When User `A` connects to an MCP server through the Identity Gateway, Aembit stores User `A`’s credentials in isolation from User `B`’s credentials.

This isolation means:

* Users can’t access each other’s MCP server sessions
* Revoking one user’s access doesn’t affect other users
* Audit trails show per-user credential usage

### Audit and workload events

[Section titled “Audit and workload events”](#audit-and-workload-events)

Workload events in Aembit Cloud**Aembit Cloud**: Aembit Cloud serves as both the central control plane and management plane, making authorization decisions, evaluating policies, coordinating credential issuance, and providing administrative interfaces for configuration.[Learn more](../../../get-started/concepts/aembit-cloud.md) capture the user identity associated with each request. This enables you to answer questions like:

* Which user accessed which MCP server and when?
* Which tools did a specific user invoke?
* Are there unusual access patterns for a particular user?

## Multi-user shared Identity Gateway

[Section titled “Multi-user shared Identity Gateway”](#multi-user-shared-identity-gateway)

The most common deployment pattern is a single MCP Identity Gateway shared by multiple users. In this model:

* All users connect to the same Identity Gateway URL
* Each user authenticates with their own IdP credentials
* The Identity Gateway identifies each user by the claims in their access token
* Aembit evaluates Access Policies and credentials per-user, not per-Identity Gateway

This works because the Identity Gateway is stateless for authentication purposes. It doesn’t maintain user sessions in the traditional sense. Instead, it extracts the user’s identity from the access token on every request and evaluates policies accordingly.

## See also

[Section titled “See also”](#see-also)

* [Blended identity](../../blended-identity.md) - How Aembit combines user and workload identity in access decisions
* [MCP Identity Gateway concepts](concepts-mcp-gateway.md) - Architecture and security model
* [Set up the MCP Identity Gateway](setup-mcp-gateway.md) - Deploy and configure the Identity Gateway
* [About MCP User-Based Access Tokens](../../../user-guide/access-policies/credential-providers/about-mcp-user-based-access-token.md) - Per-user credential management
