---
type: explanation
title: "Securing AI agent access to your resources"
description: "How Aembit secures AI agent access to enterprise resources through the Model Context Protocol (MCP)"
resource: https://docs.aembit.io/get-started/use-cases/ai-agents/
tags: ["use-case"]
timestamp: 2026-11-09T14:11:42-08:00
---

# Securing AI agent access to your resources

AI agents are fundamentally workloads, whether they are user-driven assistants like Claude Desktop or autonomous decision-makers. They authenticate to systems, request credentials, and access enterprise resources. The same identity and access management principles that secure your applications and services apply to AI agents.

Everything Aembit has built for workload IAM (attestation methods, SPIFFE compatibility, credential issuance, Access Policies) extends to agentic AI. The difference is the workload type, not the security model.

Most organizations struggle with this because they treat AI agents as a special case. They either block AI agents entirely or distribute long-lived API keys that give every user’s AI agent identical access with no visibility or control.

![Both traditional applications and AI agents authenticate through Aembit to receive short-lived credentials for enterprise resource access](https://docs.aembit.io/d2/docs/get-started/use-cases/ai-agents-0.svg)

Both traditional applications and AI agents follow the same path: authenticate with Aembit, receive short-lived credentials, and access enterprise resources under policy control.

## User-driven AI agents

AI assistants like Claude Desktop or Gemini CLI that act on behalf of an authenticated user. These AI agents have a blended identity: access ties to both the user’s identity and the AI agent’s workload identity. Policies can then scope permissions by who is using which AI agent to do what.

The rest of this page focuses on user-driven AI agents, the most mature category and the one organizations are deploying today.

## What Aembit solves

Organizations want to deploy AI agents, but security and compliance requirements block progress. Without centralized identity controls, teams wait on security reviews, AI agents lack access to internal systems, and long-lived API keys distributed as workarounds create unmonitored risk.

Aembit’s Model Context Protocol (MCP) integrations bring identity-based access control to AI agents through [blended identity](../../user-guide/access-policies/blended-identity.md), combining the user’s identity with the AI agent’s workload identity in every access decision. Instead of distributing static credentials to each AI agent, Aembit authenticates the user behind the AI agent and identifies which AI agent they are using. It then issues short-lived tokens based on your Access Policies.

Static API keys cannot scope access per AI agent or revoke access without rotation. Virtual Private Network (VPN)-based access does not distinguish AI agents from users. AI vendor built-in auth is provider-specific and not unified across systems. Aembit treats AI agents as workload identities with the same attestation, policy, and audit capabilities used for any other workload.

With Aembit, you get:

* Blended identity access control that evaluates both the user (through your IdP) and the AI agent (through workload identity) in a single access decision, not just “who is this user?” but “who is this user, using which AI agent?”
* Credential isolation, so AI agents never see the actual credentials for backend systems
* Per-user credential scoping with an MCP User-Based or Enterprise Managed Access Token Credential Provider behind MCP Identity Gateway. Each user’s requests use their own downstream credentials, even when all users share one AI agent.
* A centralized audit trail that logs every AI agent request with both user identity and AI agent identity
* Instant revocation by disabling access for a specific user, AI agent type, or user-agent combination through policy changes, not credential rotation

## Real example: securing Claude Desktop access

Your team wants to use Claude Desktop to query Jira issues and search Confluence documentation. Without Aembit, you would need to:

1. Create API tokens for Jira and Confluence
2. Distribute those tokens to every user’s Claude Desktop configuration
3. Hope nobody commits their config file to a repository
4. Accept that you have no idea which user made which API call

With Aembit, this flow looks different:

![Claude Desktop MCP authorization flow showing user authentication through identity provider, policy evaluation in Aembit, and token-based access to enterprise resources](https://docs.aembit.io/d2/docs/get-started/use-cases/ai-agents-1.svg)

When a user opens Claude Desktop and connects to your MCP server, the [MCP Authorization Server](../../user-guide/deploy-install/mcp-authorization-server/overview.md) intercepts the connection. Aembit redirects the user to your identity provider to authenticate. After the user authenticates, Aembit evaluates your [Access Policies](../concepts/access-policies.md) to determine whether this user, at this time, from this location, gets access.

If approved, Aembit issues a short-lived token that the MCP server validates. The user’s Claude Desktop session can now access Jira and Confluence through MCP. The actual API credentials never leave Aembit’s control.

How MCP OAuth 2.1 authorization works

The [Model Context Protocol (MCP) specification](https://spec.modelcontextprotocol.io/) defines how AI agents discover and connect to servers that provide context (data sources, tools, resources). MCP uses OAuth 2.1 with dynamic client registration, which means:

* AI agent clients do not need static credentials to authenticate to the MCP Authorization Server (no pre-shared secrets)
* The AI agent registers itself at runtime, receives a client ID, and uses Proof Key for Code Exchange (PKCE) to complete the OAuth flow
* After user authentication, the MCP Authorization Server issues a token with a limited lifetime (default 1 hour), which the AI agent uses to access the MCP server

This differs from traditional OAuth 2.0 by eliminating client secrets, since AI agents running on user devices cannot protect stored secrets. It also requires PKCE for all clients, not just public clients.

MCP Authorization Server vs. MCP Identity Gateway

Aembit offers two components for securing MCP traffic:

**MCP Authorization Server** Issues short-lived tokens to AI agents after user authentication. The AI agent holds the token and presents it to the MCP server on each request. Implements blended identity through a single Access Policy that evaluates both user and AI agent.

**MCP Identity Gateway** Proxies all MCP traffic through Aembit, so the AI agent never holds any credential, not even the short-lived token. Every MCP request flows through MCP Identity Gateway, which validates the user’s identity and injects credentials for each request. Extends blended identity across two Access Policies. With an MCP User-Based or Enterprise Managed Access Token Credential Provider, each user’s credentials stay isolated for each downstream MCP server.

MCP Identity Gateway provides the strongest security but introduces latency and a single point of failure. It gives zero credential exposure to the AI agent. The authorization server model is simpler and sufficient for most use cases, as the short-lived tokens expire fast and scope to specific resources. Both models implement [blended identity](../../user-guide/access-policies/blended-identity.md), combining user and workload identity in every access decision.

### Why this architecture matters for AI agents

AI agents introduce security challenges that traditional secret management does not address.

Unlike scripts or applications, AI agents make decisions about what to access and when. An AI agent might query your entire Jira backlog or search Confluence for sensitive keywords. Blended identity policies let you scope access based on both the user and the AI agent. You can allow your security team to use Claude Desktop for vulnerability data while restricting engineering to Jira and Confluence only.

As AI assistants become standard developer tools, every employee needs access to backend systems. Distributing static credentials to hundreds of AI agent configurations creates significant risk. Blended identity eliminates this problem. With an MCP User-Based or Enterprise Managed Access Token Credential Provider behind MCP Identity Gateway, each user’s requests use their own downstream credentials, which Aembit never shares across users.

When an AI agent takes an action on behalf of a user, compliance teams need to know who was responsible. Aembit’s logging ties every MCP request to both a verified user identity and the specific AI agent that made the request.

Aembit’s AI agent security is not a separate product. It leverages the same [Trust Providers](../concepts/trust-providers.md), [Credential Providers](../concepts/credential-providers.md), and [Access Policies](../concepts/access-policies.md) you use for securing service-to-service communication. The MCP Authorization Server is an integration layer on top of Aembit’s workload IAM foundation, and it extends to new AI agent types without rebuilding the security model.

### Audit and compliance for AI agents

AI agents introduce a new audit challenge. When an AI agent takes an action, compliance teams need the same answers they require for human access: who was responsible, what did they access, and when did it happen?

Because Aembit’s blended identity model captures both user and AI agent identity, every access event includes full attribution. Aembit logs every AI agent request with both verified user identity and AI agent workload identity. It treats AI agent access events the same way you would audit a human logging into a VPN or accessing a database. Logged events include:

* Authentication events, covering which user authenticated to which AI agent, at what time, from what location
* Authorization decisions, including which Access Policies evaluated, what conditions matched, and what access Aembit granted or denied
* Workload events, showing what resources the AI agent accessed, what operations it performed, and what credentials Aembit issued

Why audit for AI agents differs from traditional application logging

Traditional applications have predictable behavior: a web service queries the same database endpoints, a cron job runs the same script. AI agents make decisions in real time about what to access and how to use it. One user’s AI agent might query three Jira tickets. Another might search your entire Confluence knowledge base.

This unpredictability means you cannot rely on static application logs to understand what happened. You need identity-based audit trails that tie every AI agent action to a responsible user, just like you would for human access to sensitive systems.

Aembit’s access logs record more than “an MCP request happened.” They record that “<user@example.com>’s Claude Desktop AI agent accessed Jira at 2:34 PM, received access under policy ‘Jira-Read’, and made 3 API calls.”

For organizations with audit requirements around privileged access or regulatory compliance (SOC 2, HIPAA, PCI), this logging at the AI agent level satisfies the same controls as human access monitoring.

## Supported AI platforms

Aembit’s MCP Authorization Server supports OAuth 2.1 with dynamic client registration, which means it works with MCP-compatible AI platforms including:

* Claude Desktop, the Anthropic desktop application with MCP support
* Gemini CLI, Google’s command-line AI assistant
* Custom MCP clients (any application implementing the MCP specification)

## Next steps

### Start with the MCP Authorization Server

* [MCP Authorization Server overview](../../user-guide/deploy-install/mcp-authorization-server/overview.md) covers how OAuth 2.1 authorization works for MCP clients
* [Set up the MCP Authorization Server](../../user-guide/access-policies/mcp-authorization-server/setup-mcp-auth-server.md) walks through configuring Aembit as your MCP authorization provider

### Understand blended identity

* [Blended identity](../../user-guide/access-policies/blended-identity.md) explains how Aembit combines user and workload identity for AI agent access control

### Configure identity and policies

* [Trust Providers](../concepts/trust-providers.md) for connecting your identity provider for user authentication
* [Access Policies](../concepts/access-policies.md) for defining who can access which MCP servers and under what conditions

### Related use cases

* [MCP server access](mcp-server-access.md) covers MCP Identity Gateway, a centralized proxy that keeps credentials away from AI agents that reach many MCP servers.
* [AI and LLM Access](ai-llm-access.md) covers securing your applications’ access to AI APIs (OpenAI, Anthropic, etc.), which is distinct from securing AI agents’ access to your enterprise resources
* [Multicloud](multicloud.md) explains how Aembit provides unified workload identity across cloud providers, relevant if your AI agents access resources across AWS, Azure, and GCP
