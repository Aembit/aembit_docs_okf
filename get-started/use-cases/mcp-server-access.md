---
type: explanation
title: "Securing MCP server access"
description: "How Aembit's MCP Identity Gateway gives AI agents centralized, zero-credential access to your MCP servers."
resource: https://docs.aembit.io/get-started/use-cases/mcp-server-access/
tags: ["use-case"]
timestamp: 2026-11-03T09:49:54-08:00
---

# Securing MCP server access

Teams now connect AI agents to a growing number of MCP servers. Each connection adds another credential store, another identity to track, and another audit gap. Aembit’s MCP Identity Gateway sits in front of your MCP servers as a single, identity-aware control point.

This is the centralized, zero-credential model for MCP access. If you want the fastest path to identity-based access for the AI assistants your users run (Claude Desktop, Gemini CLI), use the MCP Authorization Server. Start with [AI agent access](ai-agents.md). Use MCP Identity Gateway when you need one audited control point across many MCP servers and users, and want AI agents to hold no downstream credentials at all.

## What Aembit solves

Without a gateway in front of your MCP servers, AI agents connect to each server directly. That creates three problems at scale:

* Every AI agent holds a credential or token for every MCP server it reaches, multiplying the secrets you have to manage.
* All users behind a shared AI agent inherit the same access, so you cannot isolate or attribute requests to a person.
* MCP activity spreads across individual AI agent configurations, with no central policy or audit trail.

MCP Identity Gateway is a data-plane proxy that Aembit operates as a managed service for your Aembit Tenant. AI agents connect to MCP Identity Gateway as if it were an MCP server. MCP Identity Gateway relays each request to the real MCP server using credentials that Aembit manages. The AI agent never receives those downstream credentials.

With Aembit, you get:

* Zero credential exposure, because AI agents never hold credentials for your MCP servers. MCP Identity Gateway holds them in memory and injects them per request.
* Per-user credential isolation, where many users share the same two Access Policies, yet each user’s requests use their own downstream credentials scoped to their identity.
* Centralized routing, where a single MCP Identity Gateway endpoint reaches multiple MCP servers, so you add or remove servers through policy instead of reconfiguring every AI agent.
* Full attribution, because MCP Identity Gateway logs every request with the user identity, the AI agent identity, the target MCP server, and the policy decision.

## How it works

AI agents connect to MCP Identity Gateway instead of connecting directly to your MCP servers. MCP Identity Gateway handles authentication and credential injection, so each request reaches the MCP server with the right credentials, and the AI agent holds none of them.

![AI agents connect to MCP Identity Gateway, which proxies each request to the MCP server using credentials the AI agent never receives](https://docs.aembit.io/d2/docs/get-started/use-cases/mcp-server-access-0.svg)

MCP Identity Gateway evaluates every MCP request against two Access Policies:

* The Client-to-Gateway policy validates which AI agent (MCP client) is connecting and authenticates the user through your identity provider with a Trust Provider.
* The Gateway-to-Server policy authorizes MCP Identity Gateway to reach a specific MCP server and uses a Credential Provider to obtain that user’s credentials for it.

Because the user’s identity rides in every request, MCP Identity Gateway extends blended identity across both Access Policies, combining the human user and the AI agent in a single access decision.

For the full request flow, token exchange, and security model, see [MCP Identity Gateway concepts](../../user-guide/deploy-install/mcp-identity-gateway/concepts-mcp-gateway.md). For a side-by-side comparison of MCP Identity Gateway and the MCP Authorization Server, see [AI agent access](ai-agents.md).

## What you can put behind MCP Identity Gateway

A single MCP Identity Gateway can front both the third-party SaaS MCP servers your AI agents consume and the MCP servers you build and operate yourself. It proxies both tool invocations and resource access, applying the same policies and credential isolation to each.

For how Aembit handles different MCP service types, including the current support and limitations for MCP apps, see [MCP servers and MCP apps](../../user-guide/deploy-install/about-mcp-servers-and-apps.md).

## Next steps

### Set up MCP Identity Gateway

* [MCP Identity Gateway overview](../../user-guide/deploy-install/mcp-identity-gateway/overview.md) covers what MCP Identity Gateway does and when to use it
* [MCP Identity Gateway concepts](../../user-guide/deploy-install/mcp-identity-gateway/concepts-mcp-gateway.md) explains the architecture, two-policy model, and security boundaries
* [Set up MCP Identity Gateway](../../user-guide/access-policies/mcp-identity-gateway/setup-mcp-gateway.md) walks through configuring your Aembit Tenant to use MCP Identity Gateway as a managed service

### Configure identity and policies

* [Trust Providers](../concepts/trust-providers.md) for authenticating the user behind each AI agent
* [Credential Providers](../concepts/credential-providers.md) for obtaining per-user credentials for each MCP server
* [Access Policies](../concepts/access-policies.md) for defining who can reach which MCP servers and under what conditions

### Understand blended identity

* [Blended identity](../../user-guide/access-policies/blended-identity.md) explains how Aembit combines user and AI agent identity in every access decision

## Related use cases

* [AI agent access](ai-agents.md) secures the AI assistants your users run, using the MCP Authorization Server for the fastest path to identity-based MCP access
* [LLM API access](ai-llm-access.md) secures your own applications’ access to LLM APIs such as OpenAI, Anthropic, and Azure OpenAI
