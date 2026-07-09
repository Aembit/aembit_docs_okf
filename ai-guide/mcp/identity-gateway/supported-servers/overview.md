---
type: reference
title: "Supported MCP Servers"
description: "Configuration guides for connecting third-party MCP servers to AI agents through the Aembit MCP Identity Gateway."
resource: https://docs.aembit.io/ai-guide/mcp/identity-gateway/supported-servers/
interface: mcp
tags: [supported-server, identity-gateway, mcp]
timestamp: 2026-06-30T20:54:29-04:00
---

# Supported MCP Servers

The Aembit MCP Identity Gateway**MCP Identity Gateway**: A component that brokers MCP traffic between MCP clients and target MCP servers, validating authorization and presenting Aembit-managed credentials on each request.[Learn more](../overview.md) brokers AI agent**AI Agent**: A software workload that authenticates to systems, requests credentials, and accesses resources, either on behalf of a person or on its own. Aembit secures AI agents with the same identity-first model it uses for any workload. User-driven agents such as Claude Desktop also carry a blended identity that ties access to both the user and the agent.[Learn more](../../../../get-started/use-cases/ai-agents.md) access to third-party MCP servers**MCP Server**: A server that implements the Model Context Protocol to provide tools, resources, or data to AI agents and MCP clients.[Learn more(opens in new tab)](https://modelcontextprotocol.io/specification/2025-03-26/server). It enforces Access Policies**Access Policy**: Access Policies define, enforce, and audit access between Client and Server Workloads by cryptographically verifying workload identity and contextual factors rather than relying on static secrets.[Learn more](../../../../get-started/concepts/access-policies.md) and injects per-user credentials, so agents never hold credentials directly.

Each of the following guides configures one MCP server to work through the Identity Gateway, using the **MCP User-Based Access Token** credential type.

MCP access vs. direct API access

These guides configure **MCP tool access through the Identity Gateway**. For direct REST API access to the same vendor (a traditional Server Workload**Server Workload**: Server Workloads represent target services, APIs, databases, or applications that receive and respond to access requests from Client Workloads.[Learn more](../../../../get-started/concepts/server-workloads.md)), see the [Server Workload guides](../../../../user-guide/access-policies/server-workloads/guides/overview.md).

## Supported servers

[Section titled “Supported servers”](#supported-servers)

Configuration guides are available for the following servers:

### Productivity

[Section titled “Productivity”](#productivity)

* [Slack](slack.md)
* [Notion](notion.md)
* [Google Workspace](google-workspace.md): Drive, Calendar, and People

### Data analytics

[Section titled “Data analytics”](#data-analytics)

* [BigQuery](bigquery.md)
