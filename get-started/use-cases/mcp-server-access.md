---
type: explanation
title: "Securing MCP server access"
description: "How Aembit's MCP Identity Gateway gives AI agents centralized, zero-credential access to your MCP servers."
resource: https://docs.aembit.io/get-started/use-cases/mcp-server-access/
tags: [use-case]
timestamp: 2026-06-15T10:19:43-07:00
---

# Securing MCP server access

Teams now connect AI agents**AI Agent**: A software workload that authenticates to systems, requests credentials, and accesses resources, either on behalf of a person or on its own. Aembit secures AI agents with the same identity-first model it uses for any workload. User-driven agents such as Claude Desktop also carry a blended identity that ties access to both the user and the agent.[Learn more](ai-agents.md) to a growing number of MCP servers**MCP Server**: A server that implements the Model Context Protocol to provide tools, resources, or data to AI agents and MCP clients.[Learn more(opens in new tab)](https://modelcontextprotocol.io/specification/2025-03-26/server). Each new connection becomes another place to store credentials, another identity to track, and another blind spot in the audit trail. Aembit’s MCP Identity Gateway**MCP Identity Gateway**: A component that brokers MCP traffic between MCP clients and target MCP servers, validating authorization and presenting Aembit-managed credentials on each request.[Learn more](../../ai-guide/mcp/identity-gateway/overview.md) addresses this by sitting in front of your MCP servers as a single, identity-aware control point for all MCP traffic.

This is the centralized, zero-credential model for MCP access. If you want the fastest path to identity-based access for the AI assistants your users run (Claude Desktop, Gemini CLI), use the MCP Authorization Server. Start with [AI agent access](ai-agents.md). Use the Identity Gateway when you need one audited control point across many MCP servers and users, and want AI agents to hold no downstream credentials at all.

## What Aembit solves

[Section titled “What Aembit solves”](#what-aembit-solves)

Without a gateway in front of your MCP servers, AI agents connect to each server directly. That creates three problems at scale:

* Every agent holds a credential or token for every MCP server it reaches, multiplying the secrets you have to manage.
* All users behind a shared agent inherit the same access, so you can’t isolate or attribute requests to a person.
* MCP activity spreads across individual agent configurations, with no central policy or audit trail.

The MCP Identity Gateway is a data-plane proxy that Aembit operates as a managed service for your Tenant. AI agents connect to the Identity Gateway as if it were an MCP server. The Identity Gateway relays each request to the real MCP server using credentials that Aembit manages. The agent never receives those downstream credentials.

With Aembit, you get:

* Zero credential exposure, because AI agents never hold credentials for your MCP servers. The Identity Gateway holds them in memory and injects them per request.
* Per-user credential isolation, where many users share the same two access policies, yet each user’s requests use their own downstream credentials scoped to their identity.
* Centralized routing, where a single Identity Gateway endpoint reaches multiple MCP servers, so you add or remove servers through policy instead of reconfiguring every agent.
* Full attribution, because the Identity Gateway logs every request with the user identity, the agent identity, the target MCP server, and the policy decision.

## How it works

[Section titled “How it works”](#how-it-works)

AI agents connect to the Identity Gateway instead of connecting directly to your MCP servers. The Identity Gateway handles authentication and credential injection, so each request reaches the MCP server with the right credentials, and the agent holds none of them.

![AI agents connect to the MCP Identity Gateway, which proxies each request to the MCP server using credentials the agent never receives](https://docs.aembit.io/d2/docs/get-started/use-cases/mcp-server-access-0.svg)

The Identity Gateway evaluates every MCP request against two Access Policies**Access Policy**: Access Policies define, enforce, and audit access between Client and Server Workloads by cryptographically verifying workload identity and contextual factors rather than relying on static secrets.[Learn more](../concepts/access-policies.md):

* The Client-to-Gateway policy validates which AI agent (MCP client**MCP Client**: An application (such as Claude Desktop, Claude Code, or Gemini CLI) that connects to MCP servers to access tools and resources on behalf of users.[Learn more](https://docs.aembit.io/user-guide/ai/mcp-auth-server/setup-mcp-auth-server/)) is connecting and authenticates the user through your identity provider with a Trust Provider**Trust Provider**: Trust Providers validate Client Workload identities through workload attestation, verifying identity claims from the workload's runtime environment rather than relying on pre-shared secrets.[Learn more](../concepts/trust-providers.md).
* The Gateway-to-Server policy authorizes the Identity Gateway to reach a specific MCP server and uses a Credential Provider**Credential Provider**: Credential Providers obtain the specific access credentials—such as API keys, OAuth tokens, or temporary cloud credentials—that Client Workloads need to authenticate to Server Workloads.[Learn more](../concepts/credential-providers.md) to obtain that user’s credentials for it.

Because the user’s identity rides in every request, the Identity Gateway extends blended identity**Blended Identity**: An access model that combines a human user's identity (authenticated through an Identity Provider) with an AI agent's workload identity into a single access decision, enabling policies that evaluate both "who is this user" and "which agent are they using" simultaneously.[Learn more](../../ai-guide/blended-identity.md) across both hops, combining the human user and the AI agent in a single access decision.

For the full request flow, token exchange, and security model, see [MCP Identity Gateway concepts](../../ai-guide/mcp/identity-gateway/concepts-mcp-gateway.md). For a side-by-side comparison of the Identity Gateway and the MCP Authorization Server, see [AI agent access](ai-agents.md).

## What you can put behind the Identity Gateway

[Section titled “What you can put behind the Identity Gateway”](#what-you-can-put-behind-the-identity-gateway)

A single Identity Gateway can front both the third-party SaaS MCP servers your agents consume and the MCP servers you build and operate yourself. It proxies both tool invocations and resource access, applying the same policies and credential isolation to each.

For how Aembit handles different MCP service types, including the current support and limitations for MCP apps, see [MCP servers and MCP apps](../../ai-guide/mcp/mcp-servers-and-apps.md).

## Next steps

[Section titled “Next steps”](#next-steps)

### Set up the Identity Gateway

[Section titled “Set up the Identity Gateway”](#set-up-the-identity-gateway)

* [MCP Identity Gateway overview](../../ai-guide/mcp/identity-gateway/overview.md) covers what the Identity Gateway does and when to use it
* [MCP Identity Gateway concepts](../../ai-guide/mcp/identity-gateway/concepts-mcp-gateway.md) explains the architecture, two-policy model, and security boundaries
* [Set up the MCP Identity Gateway](../../ai-guide/mcp/identity-gateway/setup-mcp-gateway.md) walks through configuring your Tenant for the managed Gateway

### Configure identity and policies

[Section titled “Configure identity and policies”](#configure-identity-and-policies)

* [Trust Providers](../concepts/trust-providers.md) for authenticating the user behind each agent
* [Credential Providers](../concepts/credential-providers.md) for obtaining per-user credentials for each MCP server
* [Access Policies](../concepts/access-policies.md) for defining who can reach which MCP servers and under what conditions

### Understand blended identity

[Section titled “Understand blended identity”](#understand-blended-identity)

* [Blended identity](../../ai-guide/blended-identity.md) explains how Aembit combines user and agent identity in every access decision

## Related use cases

[Section titled “Related use cases”](#related-use-cases)

* [AI agent access](ai-agents.md) secures the AI assistants your users run, using the MCP Authorization Server for the fastest path to identity-based MCP access
* [LLM API access](ai-llm-access.md) secures your own applications’ access to LLM APIs such as OpenAI, Anthropic, and Azure OpenAI
