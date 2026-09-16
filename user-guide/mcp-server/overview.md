---
type: reference
title: "Aembit MCP Server"
description: "Use the Aembit Model Context Protocol (MCP) Server to enable AI agents and users to query Aembit event logs"
resource: https://docs.aembit.io/user-guide/mcp-server/
interface: mcp
tags: ["mcp-server"]
timestamp: 2026-09-15T18:18:13-07:00
---

# Aembit MCP Server

The Aembit Model Context Protocol (MCP) Server is a secure, read-only API layer that enables AI agents and users to query Aembit event logs using structured commands. Built on the [Model Context Protocol specification](https://modelcontextprotocol.io/specification/2025-06-18), this service enables agentic observability and auditability for organizations using Aembit.

You can query audit logs, authorization events, and workload events through integrations with tools like MCP Inspector, Claude Code, GitHub Copilot, and Visual Studio. See [Audit and report on Workload activity](../audit-report/overview.md) to learn more about Aembit event logs.

## Security model

The MCP Server enforces multiple layers of access control:

* **Authentication** - API Token authentication via the `Authorization: Bearer` header. Generate tokens from the Aembit Admin UI Profile page.
* **Authorization** - Resource Set-based scoping restricts queries to resources in the assigned set, enforcing least-privilege access.
* **Read-only access** - The MCP Server supports only read operations. You can’t create, update, or delete data.
* **Audit trail** - Aembit logs all MCP Server queries, including the identity of the requesting agent.

The MCP Server provides access to operational event metadata such as timestamps, event types, and workload identifiers. It doesn’t expose secrets, credentials, or sensitive configuration data.

For more detail on how these mechanisms work, see [About the MCP Server](about-mcp-server.md).

## Pages in this section

The following pages provide information about the Aembit MCP Server:

* [About the MCP Server](about-mcp-server.md) - Learn how the MCP Server works, its capabilities, and integration scenarios
* [Connect to the MCP Server](connect/overview.md) - Prerequisites, authentication, and client-specific connection guides
* [MCP Server reference](reference-mcp-server.md) - Technical reference for endpoints, tools, resources, and example requests

**Evaluating the MCP Server for your organization?** Start with [About the MCP Server](about-mcp-server.md) for security model and access control details. **Ready to integrate?** Jump to [Connect to the MCP Server](connect/overview.md) for authentication and client setup guides.
