---
type: explanation
title: "About Content Security"
description: "Understanding Content Security Providers and their role in governing MCP tool traffic in Access Policies."
resource: https://docs.aembit.io/get-started/concepts/content-security/
tags: ["concept"]
timestamp: 2026-09-16T12:05:53-04:00
---

# About Content Security

Content Security Providers govern the Model Context Protocol (MCP) tool traffic that flows through Aembit’s MCP Identity Gateway to your MCP servers. You add a provider to an Access Policy through its Content Security component. Each provider evaluates the MCP tool messages the MCP Identity Gateway proxies, and Aembit applies the provider’s decision before the message continues.

In the Access Policy Builder, Content Security is an optional component positioned between Access Conditions and Credential Providers. A policy evaluates its Content Security Provider on each matching request, adding an evaluation step to the request path without changing how the Access Policy’s other components authorize access.

![](https://docs.aembit.io/aembit-icons/content-security.svg)

[Start configuring Content Security](../../user-guide/access-policies/content-security/overview.md)See Content Security in the User Guide

→

## How Content Security works

A Content Security Provider acts on the two things an AI agent does with the MCP tools an MCP server exposes:

* **What an AI agent discovers** - The set of MCP tools an MCP server advertises when an AI agent asks what MCP tools exist.
* **What an AI agent calls** - The calls an AI agent makes to those MCP tools.

Both map to standard MCP methods: `tools/list` and `tools/call`. See [Proxied MCP methods](../../user-guide/deploy-install/mcp-identity-gateway/reference-mcp-gateway.md#proxied-mcp-methods).

A provider runs after the MCP Identity Gateway validates the AI agent’s token and after Aembit Cloud evaluates the Access Policies. When those checks succeed and the Access Policy includes a Content Security Provider, the MCP Identity Gateway applies the provider’s decision. It applies that decision before it forwards a request to the MCP server, and before it relays a response to the AI agent.

When an Access Policy carries both Content Security Providers, each one acts on something different. They run in a fixed order on both the request path and the response path.

![Both Content Security Providers evaluating MCP traffic in the MCP Identity Gateway: on the request path MCP Tool Access Control checks a tool call before CrowdStrike AIDR inspects its inputs, and on the response path MCP Tool Access Control removes hidden tools from a listing before CrowdStrike AIDR inspects what remains](https://docs.aembit.io/d2/docs/get-started/concepts/content-security-0.svg)

The MCP Identity Gateway checks a tool call on the request path, before it forwards the call to the MCP server. It filters a tool listing on the response path, after the MCP server returns the listing and before the AI agent sees it. MCP Tool Access Control matches tool names first in both directions, so CrowdStrike AIDR inspects only what the tool rules already allowed.

The MCP Identity Gateway proxies MCP requests between AI agents and MCP servers, not traffic between AI agents and large language model (LLM) providers. Prompts and completions exchanged with an LLM never pass through a Content Security Provider.

Aembit records every decision a provider makes. MCP workload events record the decision for each message under `application.mcp.contentSecurity`, and the `access.authorization` event names the provider Aembit identified for the Access Policy. See [MCP workload events](../../user-guide/audit-report/workload-events/supported-protocols.md#content-security) and [Access Authorization Events](../../user-guide/audit-report/access-authorization-events.md#content-security).

## Inspection scope

No message a Content Security Provider evaluates reaches its destination without a decision. A provider doesn’t correlate messages or track a task across the requests an AI agent makes to complete it. A provider also applies only to the Access Policies you add it to, so its scope is the traffic those Access Policies govern.

## CrowdStrike AIDR and the CrowdStrike Access Condition

Aembit integrates with two different CrowdStrike products through two different Access Policy components. CrowdStrike AIDR is a Content Security Provider that inspects MCP content and returns a verdict Aembit enforces. The CrowdStrike [Access Condition](../../user-guide/access-policies/access-conditions/crowdstrike.md) evaluates endpoint posture, such as the reported state of a Client Workload’s endpoint. Configuring one doesn’t enable the other.

For how AIDR evaluates content, its failure behavior, and where to get credentials, see [CrowdStrike AIDR Content Security](../../user-guide/access-policies/content-security/crowdstrike-aidr/overview.md). To add it to an Access Policy, see [Add CrowdStrike AIDR to an Access Policy](../../user-guide/access-policies/content-security/crowdstrike-aidr/add-to-policy.md).

## MCP Tool Access Control

MCP Tool Access Control decides which MCP tools an AI agent can reach. It applies two controls that work independently: Tool Visibility and Tool Invocation.

* **Tool Visibility** - Which MCP tools an AI agent discovers when it requests a tool listing.
* **Tool Invocation** - Which MCP tools an AI agent can call.

Each control either allows or blocks its MCP tools, and applies to every MCP tool or to a specific list you build. Because the controls are independent, hiding an MCP tool from a listing doesn’t stop an AI agent that already knows the name from calling it.

For how the two controls combine, see [MCP Tool Access Control](../../user-guide/access-policies/content-security/mcp-tool-access-control/overview.md). To add it to an Access Policy, see [Add MCP Tool Access Control to an Access Policy](../../user-guide/access-policies/content-security/mcp-tool-access-control/add-to-policy.md). For the glob syntax and limits that apply to MCP tool names, see [MCP tool name reference](../../user-guide/access-policies/content-security/mcp-tool-access-control/reference.md).

## Data sharing and privacy

When an Access Policy includes CrowdStrike AIDR, Aembit sends the inspected MCP content and request metadata to CrowdStrike AIDR. Review CrowdStrike’s data handling before you add CrowdStrike AIDR to Access Policies that carry sensitive content. For exactly what Aembit sends, see [Data shared for inspection](../../user-guide/access-policies/content-security/crowdstrike-aidr/overview.md#data-shared-for-inspection).

MCP Tool Access Control evaluates your rules within Aembit, so it sends no MCP content to a third party for inspection.
