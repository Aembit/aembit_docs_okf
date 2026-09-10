---
type: explanation
title: "Content Security in the MCP Identity Gateway"
description: "How Content Security Providers govern MCP tool traffic in the MCP Identity Gateway request path."
resource: https://docs.aembit.io/ai-guide/mcp/identity-gateway/content-security-mcp-gateway/
tags: ["identity-gateway", "mcp"]
timestamp: 2026-09-09T08:20:13-07:00
---

# Content Security in the MCP Identity Gateway

The MCP Identity Gateway applies Content Security Providers to the MCP tool traffic it proxies. You add a provider to the Gateway-to-Server Access Policy, and the Gateway applies that provider’s decision to each MCP tool message the policy matches. This page explains where a provider sits in the MCP Identity Gateway request path, what it acts on, and what its decision does to MCP tool traffic.

For the component itself, see [Content Security](../../../user-guide/access-policies/content-security/overview.md).

## Where evaluation happens

Evaluation runs in the MCP Identity Gateway’s data plane, after identity checks and before content moves on. On each MCP request, the MCP Identity Gateway validates the AI agent’s token and Aembit Cloud evaluates the Access Policies as usual. When those checks succeed and the policy includes a Content Security Provider, the MCP Identity Gateway applies the provider’s decision before it forwards the content to the MCP server or relays it back to the AI agent.

Evaluation adds a step to the request path without changing how the MCP Identity Gateway authenticates and authorizes MCP traffic.

## What a Content Security Provider acts on

A provider acts on the two things an AI agent does with the MCP tools an MCP server exposes:

* **What an AI agent discovers** - The set of MCP tools an MCP server advertises when an agent asks what MCP tools exist.
* **What an AI agent calls** - The calls an agent makes to those MCP tools.

Both map to standard MCP methods: `tools/list` and `tools/call`. See [Proxied MCP methods](reference-mcp-gateway.md#proxied-mcp-methods).

The MCP Identity Gateway proxies MCP requests between AI agents and MCP servers, not traffic between AI agents and large language model (LLM) providers. Prompts and completions exchanged with an LLM never pass through a Content Security Provider.

## What a Content Security Provider’s decision does

A provider evaluates each MCP tool message on its own, and a decision covers only the message it applies to, not the conversation, session, or task that message belongs to.

When a provider blocks an MCP tool message, the MCP Identity Gateway stops it there. It doesn’t forward a blocked request to the MCP server, and it doesn’t relay blocked content to the AI agent.

Aembit records every decision in an MCP workload event under `application.mcp.contentSecurity`, keyed by the provider that decided. For the fields each provider records, see [MCP workload events](../../../user-guide/audit-report/workload-events/supported-protocols.md#content-security).

## CrowdStrike AIDR in the MCP Identity Gateway request path

CrowdStrike AI Detection and Response (AIDR) inspects MCP content at three checkpoints and returns a verdict that the MCP Identity Gateway enforces. The following diagram shows the inspection path for an MCP tool listing and an MCP tool call. Token validation and Access Policy evaluation complete before inspection starts, so the diagram shows only the inspection path.

![CrowdStrike AIDR inspection sequence showing the three checkpoints and the verdict returned at each](https://docs.aembit.io/d2/docs/ai-guide/mcp/identity-gateway/content-security-mcp-gateway-0.svg)

The MCP Identity Gateway inspects MCP tool listings and MCP tool call outputs on the response path, before it relays content to the AI agent. It inspects MCP tool call inputs on the request path, before it forwards the call to the MCP server. When AIDR’s verdict is **Block**, the MCP Identity Gateway returns an error response to the AI agent in place of the content.

Each checkpoint adds a call to AIDR on the request path, which adds latency to inspected MCP traffic. The **Timeout** and **Max Retries** settings bound how long the MCP Identity Gateway waits for a verdict, and **Fail Open on Error** determines what the MCP Identity Gateway does when it can’t reach AIDR at all.

For the checkpoints and verdicts in full, the failure behavior, the content size limit, and where to get credentials, see [CrowdStrike AIDR Content Security](../../../user-guide/access-policies/content-security/crowdstrike-aidr/overview.md). To add it to a policy, see [Add CrowdStrike AIDR to a policy](../../../user-guide/access-policies/content-security/crowdstrike-aidr/add-to-policy.md).
