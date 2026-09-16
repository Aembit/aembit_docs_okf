---
type: explanation
title: "MCP Tool Access Control"
description: "A Content Security Provider that controls which MCP tools an AI agent can discover and call."
resource: https://docs.aembit.io/user-guide/access-policies/content-security/mcp-tool-access-control/
tags: ["mcp-tool-access-control", "content-security", "access-policy"]
timestamp: 2026-09-16T11:10:21-07:00
---

# MCP Tool Access Control

MCP Tool Access Control is a Content Security Provider that governs which Model Context Protocol (MCP) tools an AI agent can discover and which it can call.

Authorizing an MCP Server grants an AI agent access to every MCP tool that the MCP server exposes. MCP Tool Access Control lets you control that access. You choose which MCP tools to allow and which to block. The MCP Identity Gateway enforces your choices on every request your Access Policy governs.

An Access Policy can carry both MCP Tool Access Control and CrowdStrike AI Detection and Response (AIDR). MCP Tool Access Control matches tool names first, so AIDR inspects only the MCP tools and calls that MCP Tool Access Control allowed.

> **Note**
>
> MCP Tool Access Control isn’t enabled by default. Contact [Aembit support](https://aembit.io/contact) to enable this feature.

To add MCP Tool Access Control to an Access Policy, see [Add MCP Tool Access Control to an Access Policy](add-to-policy.md).

## What AI agents can discover and call

MCP Tool Access Control governs how AI agents discover MCP tools and how they call them. You add the MCP tools you want to govern, then allow or block them.

* The Tool Visibility control decides which MCP tools an AI agent discovers when it asks an MCP server what MCP tools exist. A blocked MCP tool never appears in that listing.
* The Tool Invocation control decides which MCP tools an AI agent can call. The MCP Identity Gateway returns an error for a call to a blocked tool and never forwards it to the MCP server.

Both controls act on standard MCP methods, `tools/list` and `tools/call`. See [Proxied MCP methods](../../../deploy-install/mcp-identity-gateway/reference-mcp-gateway.md#proxied-mcp-methods).

Aembit records what each control decided in the Workload Event, so an event shows why an AI agent didn’t discover an MCP tool or why a call failed.

> **Hide and block to fully deny an MCP tool**
>
> Tool Visibility and Tool Invocation evaluate independently.
>
> Tool Visibility can block an AI agent from discovering a tool. Tool Invocation can block an AI agent from calling an MCP tool it knows about.
>
> If an AI agent already knows about an MCP tool you blocked with Tool Visibility alone, it can still call it. Block an MCP tool in both controls when you want to be certain an AI agent can’t call it.

## MCP tool names

Aembit doesn’t collect or store the MCP tool list from an MCP server, so you need to know the MCP tool names you want to govern. Take them from the MCP server’s documentation, which lists the names it publishes, such as `get_issue`.

> **An AI agent’s MCP tool list shows different names**
>
> The MCP Identity Gateway prefixes every MCP tool with the Server Workload it came from, so the names an AI agent displays don’t match what the MCP server publishes. Use the name the MCP server publishes before the MCP Identity Gateway prefixes it or the entry can’t match.

You can add an exact name or a glob pattern, and matching is case-sensitive. For the full syntax and the field limits, see [MCP Tool Access Control tool name reference](reference.md).
