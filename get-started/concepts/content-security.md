---
type: explanation
title: "About Content Security"
description: "Understanding Content Security Providers and their role in governing MCP tool traffic in Access Policies."
resource: https://docs.aembit.io/get-started/concepts/content-security/
tags: ["concept"]
timestamp: 2026-09-09T08:20:13-07:00
---

# About Content Security

Content Security Providers govern the Model Context Protocol (MCP) tool traffic that flows through Aembit’s MCP Identity Gateway to your MCP servers. You add a provider to an Access Policy through its Content Security component. Each provider evaluates the MCP tool messages the MCP Identity Gateway proxies, and Aembit applies the provider’s decision before the message continues.

In the Access Policy Builder, Content Security is an optional component positioned between Access Conditions and Credential Providers. A policy evaluates its Content Security Provider on each matching request, adding an evaluation step to the request path without changing how the policy’s other components authorize access.

![](https://docs.aembit.io/aembit-icons/content-security.svg)

[Start configuring Content Security](../../user-guide/access-policies/content-security/overview.md)See Content Security in the User Guide

→

## How Content Security works

A Content Security Provider acts on the two things an AI agent does with the MCP tools an MCP server exposes:

* **What an AI agent discovers** - The set of MCP tools an MCP server advertises when an agent asks what MCP tools exist.
* **What an AI agent calls** - The calls an agent makes to those MCP tools.

Both map to standard MCP methods: `tools/list` and `tools/call`. See [Proxied MCP methods](../../ai-guide/mcp/identity-gateway/reference-mcp-gateway.md#proxied-mcp-methods).

A provider runs after the MCP Identity Gateway validates the AI agent’s token and after Aembit Cloud evaluates the Access Policies. When those checks succeed and the policy includes a Content Security Provider, the MCP Identity Gateway applies the provider’s decision before it forwards a request to the MCP server or relays a response back to the AI agent.

The MCP Identity Gateway proxies MCP requests between AI agents and MCP servers, not traffic between AI agents and large language model (LLM) providers. Prompts and completions exchanged with an LLM never pass through a Content Security Provider.

Aembit records every decision a provider makes. MCP workload events record the decision for each message under `application.mcp.contentSecurity`, and the `access.authorization` event names the provider Aembit identified for the policy. See [MCP workload events](../../user-guide/audit-report/workload-events/supported-protocols.md#content-security) and [Access Authorization Events](../../user-guide/audit-report/access-authorization-events.md#content-security).

## Inspection scope

No message a Content Security Provider evaluates reaches its destination without a decision. A provider doesn’t correlate messages or track a task across the requests an AI agent makes to complete it. A provider also applies only to the policies you add it to, so its scope is the traffic those policies govern.

## CrowdStrike AIDR and the CrowdStrike Access Condition

Aembit integrates with two different CrowdStrike products through two different Access Policy components. CrowdStrike AIDR is a Content Security Provider that inspects MCP content and returns a verdict Aembit enforces. The CrowdStrike [Access Condition](../../user-guide/access-policies/access-conditions/crowdstrike.md) evaluates endpoint posture, such as the reported state of a Client Workload’s endpoint. Configuring one doesn’t enable the other.

For how AIDR evaluates content, its failure behavior, and where to get credentials, see [CrowdStrike AIDR Content Security](../../user-guide/access-policies/content-security/crowdstrike-aidr/overview.md). To add it to a policy, see [Add CrowdStrike AIDR to a policy](../../user-guide/access-policies/content-security/crowdstrike-aidr/add-to-policy.md).

## Data sharing and privacy

When a policy includes CrowdStrike AIDR, Aembit sends the inspected MCP content and request metadata to CrowdStrike AIDR. Review CrowdStrike’s data handling before you add CrowdStrike AIDR to policies that carry sensitive content. For exactly what Aembit sends, see [Data shared for inspection](../../user-guide/access-policies/content-security/crowdstrike-aidr/overview.md#data-shared-for-inspection).
