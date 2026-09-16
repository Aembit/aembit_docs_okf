---
type: reference
title: "MCP overview"
description: "Overview of Aembit's Model Context Protocol (MCP) components"
resource: https://docs.aembit.io/user-guide/deploy-install/about-mcp/
interface: mcp
tags: ["deploy-install"]
timestamp: 2026-09-15T18:18:13-07:00
---

# MCP overview

Aembit provides components for securing AI agent communications using the Model Context Protocol (MCP). These components enable AI agents to authenticate and access protected resources using workload identity.

## Components

| Component                                                                    | Description                                                                                                                   | Status    |
| ---------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- | --------- |
| [Authorization Server](mcp-authorization-server/overview.md) | OAuth 2.1 authorization server for MCP clients                                                                                | Available |
| [Identity Gateway](mcp-identity-gateway/overview.md)         | Transparent proxy that enforces access policies and injects workload identity credentials into MCP tool and resource requests | Available |

## Concepts

* [MCP servers and MCP apps](about-mcp-servers-and-apps.md) - How Aembit handles MCP servers and MCP apps differently

## Troubleshooting and observability

* [Troubleshoot MCP and AI IAM access](../troubleshooting/mcp-ai-iam.md) - End-to-end investigation guide that ties Access Authorization Events, Workload Events, and MCP Authorization Tracing together
* [MCP Authorization Tracing](../audit-report/mcp-authorization-tracing.md) - Live view of inbound MCP authorization requests at the Gateway
