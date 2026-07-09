---
type: explanation
title: "About the Aembit MCP Server"
description: "Learn how the Aembit MCP Server provides a secure, read-only API layer for AI agents to query Aembit event logs."
resource: https://docs.aembit.io/ai-guide/mcp/mcp-server/about-mcp-server/
tags: [mcp-server, mcp]
timestamp: 2026-02-24T15:57:13-08:00
---

# About the Aembit MCP Server

The Aembit MCP Server is a read-only API service that lets AI agents and integrations query Aembit event logs using the Model Context Protocol (MCP) open standard.

Read-only access

The Aembit MCP Server only supports read operations. You can’t create, update, or delete data through the MCP Server.

## What Aembit’s MCP Server does

[Section titled “What Aembit’s MCP Server does”](#what-aembits-mcp-server-does)

The Aembit MCP Server is a secure, read-only API layer that enables AI agents and users to query Aembit event logs using high-level, structured commands. You can query audit, authorization, and workload logs. It’s built on the [Model Context Protocol specification](https://modelcontextprotocol.io/specification/2025-06-18/server/tools), an open standard for agentic AI integrations. This service brings observability, auditability, and automation to organizations using Aembit.

## Why use Aembit’s MCP Server

[Section titled “Why use Aembit’s MCP Server”](#why-use-aembits-mcp-server)

The MCP Server provides:

* Visibility into security and operational events for compliance and audit needs.
* Automation and AI-driven insights, reducing manual investigation and reporting.
* Secure, governed access to sensitive event data.
* Custom CLI tools, dashboards, or chatbots that interact directly with Aembit event data.
* Integration with agentic workflows and AI assistants.
* Rapid, programmatic access to logs for troubleshooting, monitoring, and reporting.

## How does it work?

[Section titled “How does it work?”](#how-does-it-work)

* **Authentication:** Users or AI agents authenticate using an Aembit API Token, which you can generate from the Admin UI Profile page.

* **MCP Service URL:** Send structured queries to the MCP Service URL:

  ```shell
  https://<tenantId>.mcp.useast2.aembit.io/mcp
  ```

  Replace `<tenantId>` with your tenant identifier, visible in the Aembit Admin UI Profile screen.

* **Resource Sets:** Resource Sets**Resource Set**: Resource Sets are organizational containers that group Access Policy components together, enabling you to manage configurations across different environments, regions, or use cases.[Learn more](../../../user-guide/administration/resource-sets/overview.md) govern which event data your queries can access, providing separation of concerns and least-privilege access. If your API token targets a specific Resource Set, you can only query logs for resources in that set. [Learn more about Resource Sets](../../../user-guide/administration/resource-sets/overview.md).

* **Audit Logging:** Aembit logs all queries to the MCP Server in the audit trail, including the identity of the requesting agent.

* **Manifest/Tool Definition:** The MCP Server exposes a machine-readable manifest (tool definition) that you can register with agent frameworks such as LangChain or OpenAI function calling. This allows agents to discover available tools and their parameters programmatically. See the [MCP specification](https://modelcontextprotocol.io/specification/2025-06-18) for more details.

* **No Natural Language Parsing:** The MCP Server expects structured JSON commands, not free-form natural language. Your agent or integration must translate user queries into the supported tool format.

## Supported tools and resources

[Section titled “Supported tools and resources”](#supported-tools-and-resources)

* **Tools:**

  * `get_audit_logs`: Retrieve audit logs (filtering, pagination)
  * `get_auth_events`: Retrieve authorization events
  * `get_workload_events`: Retrieve workload**Workload**: Any non-human entity (application, service, automation, AI agent, etc.) that needs to access resources.[Learn more](https://docs.aembit.io/get-started/concepts/how-aembit-works/#introducing-workload-iam) events

* **Resources:**

  * Audit Log Severities
  * Audit Log Categories
  * Authorization Event Types
  * Authorization Event Severities
  * Workload Event Types
  * Workload Event Severities

* **General MCP Protocol Documentation:**

  * [MCP Tools](https://modelcontextprotocol.io/specification/2025-06-18/server/tools)
  * [MCP Resources](https://modelcontextprotocol.io/specification/2025-06-18/server/resources)

## Integration scenarios

[Section titled “Integration scenarios”](#integration-scenarios)

* **DevOps/Security:** Build custom CLI tooling to query and summarize Aembit event logs for compliance, troubleshooting, or monitoring. Integrate with agentic workflows to automate event visibility and reporting.
* **Developers:** Create dashboards or chatbots that interface directly with Aembit logs, enabling rapid insights and alerts.
* **AI/ML Teams:** Enable AI agents to analyze, summarize, or act on Aembit event data as part of broader automation or security workflows.

## Example prompts

[Section titled “Example prompts”](#example-prompts)

See the [Prompt Library](../../prompt-library/overview.md) for curated prompts you can use with the MCP Server, including:

* Authentication activity monitoring
* Authorization event investigation
* Workload event analysis

The library includes ready-to-use prompts and guidance on customizing them for your specific needs.

## Security considerations

[Section titled “Security considerations”](#security-considerations)

* **Token scope** - API tokens grant read-only access to audit, authorization, and workload event logs. Tokens can’t create, modify, or delete any data.
* **Data exposure** - Queries return operational metadata such as timestamps, event types, and workload identifiers. The MCP Server doesn’t expose secrets, credentials, or sensitive configuration data.
* **Audit logging** - Aembit logs all MCP Server queries in the audit trail, including the identity of the requesting agent.
* **Rate limiting** - The MCP Server limits concurrent requests per source IP and tenant to prevent abuse.

## Next steps

[Section titled “Next steps”](#next-steps)

* [Connect to the MCP Server](connect/overview.md) - Authentication setup and client-specific connection guides
* [MCP Server reference](reference-mcp-server.md) - Complete tool schemas, parameters, and example requests
* [Prompt Library](../../prompt-library/overview.md) - Curated prompts and prompt engineering best practices

## Limitations and release status

[Section titled “Limitations and release status”](#limitations-and-release-status)

* **Read-only access:** The Aembit MCP Server supports only read-only operations. You can’t create, update, or delete data using this server.
* **Release status:**
  * Your Aembit administrator must enable the MCP Server for your tenant
