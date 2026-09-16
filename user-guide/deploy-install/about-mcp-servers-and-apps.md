---
type: explanation
title: "MCP servers and MCP apps"
description: "How Aembit handles MCP servers and MCP apps differently, and what to expect when connecting each through the MCP Identity Gateway."
resource: https://docs.aembit.io/user-guide/deploy-install/about-mcp-servers-and-apps/
tags: ["deploy-install"]
timestamp: 2026-09-15T18:18:13-07:00
---

# MCP servers and MCP apps

The MCP ecosystem includes two types of services that AI agents connect to: **MCP servers** and **MCP apps**. While both use the Model Context Protocol, they expose different capabilities and behave differently when connected through the Aembit MCP Identity Gateway.

This page explains the distinction and what Aembit supports for each. For the official specification, see the [MCP Apps overview](https://modelcontextprotocol.io/extensions/apps/overview) in the MCP documentation.

## What MCP servers expose

A traditional MCP Server exposes **tools** that AI agents can invoke. Tools are actions like querying a database, sending an email, or creating a Jira ticket. The AI agent (LLM) decides when to call a tool and provides the required parameters.

Most MCP servers in use today expose only tools. The Aembit MCP Identity Gateway fully supports tool discovery (`tools/list`) and invocation (`tools/call`) across all assigned MCP servers.

## What MCP apps are

An MCP App is an interactive UI application that renders inside an MCP host such as Claude Desktop or ChatGPT. MCP Apps are an [extension to the core MCP specification](https://modelcontextprotocol.io/extensions/apps/overview), not part of the base protocol.

An MCP App combines two MCP primitives. A **tool** declares a UI resource in its description, through a `_meta.ui.resourceUri` field that points to a `ui://` resource. A **resource** holds the HTML page the host renders in an isolated iframe. MCP servers can then return interactive interfaces, such as data visualizations, forms, and dashboards, directly in the chat conversation.

In short:

* **MCP servers** expose tools (actions the LLM invokes)
* **MCP apps** extend MCP servers with interactive HTML interfaces that render inside the host

## How the Identity Gateway handles apps vs servers

This section describes how Aembit’s MCP Identity Gateway supports MCP servers and MCP apps, including any limitations or expected behaviors when connecting each type through the Identity Gateway.

### Tool support (fully supported)

The Identity Gateway proxies tool discovery and invocation for both MCP servers and MCP apps. When an MCP client requests `tools/list`, the Identity Gateway fans out the request across all assigned MCP servers. It aggregates the results and returns them with server-specific prefixes to prevent name collisions.

The Identity Gateway also preserves tool annotations in `tools/list` responses. Annotations categorize tools by behavior (such as read-only or destructive operations), and MCP clients use them to organize tools in their UI.

### Resource support (supported with limitations)

The Identity Gateway supports MCP resource discovery and retrieval:

| Method           | Description                                                                                                                  |
| ---------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| `resources/list` | Discovers available resources across all assigned MCP servers. Returns resource URIs as-is without server-specific prefixes. |
| `resources/read` | Retrieves a specific resource by URI from the appropriate MCP server.                                                        |

Resources go through the same Access Policy evaluation, authentication, and credential injection as tool requests. Aembit doesn’t require any special configuration to support resources from MCP apps.

> **No resource prefixing**
>
> Unlike tool names, the Identity Gateway doesn’t prefix resource URIs with server identifiers. If two MCP servers expose resources with the same URI, both appear in the aggregated list.

### MCP apps (partial support)

The Identity Gateway can proxy tool and resource requests from MCP apps. One limitation exists: the Identity Gateway adds server-specific prefixes to tool names, which can conflict with the `_meta.ui.resourceUri` references that MCP apps use to link tools to their UI resources.

In practice, this means:

* Tools from MCP apps work as expected through the Identity Gateway
* Resources from MCP apps are accessible
* Interactive UI experiences may not render correctly if the host can’t match the prefixed tool name to its declared UI resource

### What to expect with unsupported capabilities

If an MCP server doesn’t support resources, the Identity Gateway handles this cleanly. The `resources/list` request returns an empty list for that server without generating errors.

If you connect an MCP app and its UI features don’t work as expected, this is likely due to the tool-naming limitation described in the previous section. The tools and resources themselves remain accessible individually.

## How AI models use resources in practice

Current AI models (such as Claude and Gemini) tend to access resources indirectly through tools rather than through explicit `resources/read` calls. For example, when asked to fetch a resource, the model typically invokes a tool like `get_resource` exposed by the MCP server rather than calling `resources/read` directly.

This behavior is evolving as both the MCP specification and AI model capabilities mature. The Identity Gateway supports both access patterns.

## See also

* [MCP Apps overview](https://modelcontextprotocol.io/extensions/apps/overview) - Official MCP specification for apps
* [MCP Identity Gateway concepts](mcp-identity-gateway/concepts-mcp-gateway.md) - Architecture and resource support details
* [Proxied MCP methods](mcp-identity-gateway/reference-mcp-gateway.md#proxied-mcp-methods) - Complete list of MCP methods the Identity Gateway proxies
