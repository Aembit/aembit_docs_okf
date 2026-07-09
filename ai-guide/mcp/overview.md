---
type: explanation
title: "MCP overview"
description: "Overview of Aembit's Model Context Protocol (MCP) components"
resource: https://docs.aembit.io/ai-guide/mcp/
tags: [mcp]
timestamp: 2026-06-18T13:15:52-04:00
type_inferred: true
---

# MCP overview

Aembit provides components for securing AI agent communications using the Model Context Protocol (MCP). These components enable AI agents to authenticate and access protected resources using workload identity.

## Components

[Section titled “Components”](#components)

| Component                                                   | Description                                                                                                                   | Status    |
| ----------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- | --------- |
| [Authorization Server](authorization-server/overview.md) | OAuth 2.1 authorization server for MCP clients                                                                                | Available |
| [Identity Gateway](identity-gateway/overview.md)         | Transparent proxy that enforces access policies and injects workload identity credentials into MCP tool and resource requests | Available |

## Concepts

[Section titled “Concepts”](#concepts)

* [MCP servers and MCP apps](mcp-servers-and-apps.md) - How Aembit handles MCP servers and MCP apps differently

## Troubleshooting and observability

[Section titled “Troubleshooting and observability”](#troubleshooting-and-observability)

* [Troubleshoot MCP and AI IAM access](../../user-guide/troubleshooting/mcp-ai-iam.md) - End-to-end investigation guide that ties Access Authorization Events, Workload Events, and MCP Authorization Tracing together
* [MCP Authorization Tracing](../../user-guide/audit-report/mcp-authorization-tracing.md) - Live view of inbound MCP authorization requests at the Gateway
