---
type: explanation
title: "About the MCP User-Based Access Token Credential Provider"
description: "How the MCP User-Based Access Token Credential Provider manages per-user OAuth credentials for MCP server access"
resource: https://docs.aembit.io/user-guide/access-policies/credential-providers/about-mcp-user-based-access-token/
tags: ["credential-provider", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# About the MCP User-Based Access Token Credential Provider

The MCP User-Based Access Token Credential Provider retrieves and manages OAuth 2.0 credentials on a per-user basis for [Model Context Protocol (MCP)](../../../ai-guide/mcp/overview.md) server access. The standard [OAuth 2.0 Authorization Code](oauth-authorization-code.md) Credential Provider stores tokens for the Aembit administrator who authorizes the integration. This type stores tokens for each end user who authenticates through the MCP authorization flow.

Use this Credential Provider with the [MCP Identity Gateway](../../../ai-guide/mcp/identity-gateway/overview.md), where multiple users access MCP servers through a shared Gateway. Each user completes their own OAuth consent flow, and Aembit stores and rotates their tokens individually.

See [Configure MCP User-Based Access Token](mcp-user-based-access-token.md) to create one.

## How it works

When a user first accesses an MCP server through the MCP Identity Gateway:

1. Aembit detects that no credentials exist for that user and MCP server.
2. Aembit redirects the user to the MCP server’s authorization endpoint to authenticate and grant consent.
3. Aembit stores the resulting access and refresh tokens, associating them with that specific user.
4. On subsequent requests, Aembit retrieves the user’s stored credentials and handles token refresh automatically.

Unlike the standard OAuth 2.0 Authorization Code Credential Provider, this type doesn’t require an administrator to click **Authorize** during setup. Instead, each user completes their own OAuth consent flow the first time they access the MCP server through the Gateway.

## When to use this type

**Required** — Use **MCP User-Based Access Token** when:

* You are configuring a **Gateway-to-Server** Access Policy for the [MCP Identity Gateway](../../../ai-guide/mcp/identity-gateway/overview.md) and need per-user credential isolation
* The MCP server requires per-user OAuth credentials (most SaaS MCP servers)
* Multiple users share the same Access Policy but each user must authenticate individually

This is the standard Credential Provider type for Gateway-to-Server policies. Most MCP Identity Gateway deployments require it because SaaS MCP servers issue credentials scoped to individual users.

**Not required** — Use **[OAuth 2.0 Authorization Code](oauth-authorization-code.md)** instead when:

* An Aembit administrator authorizes the integration once and all users share those credentials
* The MCP server accepts a single shared credential (for example, a service account)
* Per-user credential isolation isn’t needed

## Role in MCP Identity Gateway

The [MCP Identity Gateway](../../../ai-guide/mcp/identity-gateway/overview.md) requires a Credential Provider for its Gateway-to-Server Access Policy. Which type you use depends on whether the MCP server issues credentials per user or accepts a shared credential:

* **Per-user credentials (most SaaS MCP servers)** — Use MCP User-Based Access Token. The MCP server issues OAuth tokens scoped to individual users, so each person must complete their own authorization flow. This is the standard configuration for most Gateway deployments.
* **Shared credentials** — Use [OAuth 2.0 Authorization Code](oauth-authorization-code.md) or [Aembit Access Token](aembit-access-token.md). An administrator authorizes the integration once, and all users share those credentials. This works when the MCP server accepts a single service account or shared token.

### Why per-user credentials matter

The MCP Identity Gateway enforces a separation between the credentials AI agents use and the credentials for MCP servers:

* **Agent-to-Gateway** — The AI agent holds a token that identifies both the agent and the user. This token is only valid against the MCP Identity Gateway.
* **Gateway-to-MCP server** — The Credential Provider manages tokens that are valid against the MCP server. The Gateway never exposes these tokens to AI agents.

MCP traffic carries two identities: the MCP client (workload) identity and the user (human) identity. The MCP client identity determines *which* AI agents can connect, while the user identity determines *who* can access which MCP servers. When you use MCP User-Based Access Token, the Credential Provider uses the user identity to store and retrieve the correct OAuth tokens for each individual — maintaining credential isolation even though all users share the same Access Policy.

For the full architectural explanation, see [MCP Identity Gateway concepts](../../../ai-guide/mcp/identity-gateway/concepts-mcp-gateway.md).

## Related topics

* [Configure MCP User-Based Access Token](mcp-user-based-access-token.md) - Step-by-step configuration guide
* [MCP Identity Gateway setup](../../../ai-guide/mcp/identity-gateway/setup-mcp-gateway.md) - How to configure Gateway-to-Server Access Policies that use this Credential Provider
* [MCP Identity Gateway concepts](../../../ai-guide/mcp/identity-gateway/concepts-mcp-gateway.md) - How the MCP Identity Gateway uses per-user credentials
