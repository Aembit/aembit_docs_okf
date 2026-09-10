---
type: reference
title: "Prompt Library"
description: "Curated prompts for querying Aembit event logs through the MCP Server."
resource: https://docs.aembit.io/ai-guide/prompt-library/
interface: mcp
tags: ["prompt-library"]
timestamp: 2026-02-24T15:57:13-08:00
---

# Prompt Library

The Prompt Library provides curated prompts for working with the [Aembit MCP Server](../mcp/mcp-server/overview.md). Use these prompts as starting points when querying audit logs, authorization events, and workload events through AI assistants and MCP-compatible clients.

This page assumes you have configured the MCP Server. See [Connect to the MCP Server](../mcp/mcp-server/connect/overview.md) for authentication and setup.

> **Prompt precision**
>
> Prompts must be precise and structured. The MCP Server checks for specific fields and may not understand ambiguous or vague requests.
>
> For example, instead of “Show me recent security events,” specify the event type and time range: “Summarize authorization event activity for the 8am EST hour this morning.”

## Authentication activity

Audit events track administrative actions and system events in your Aembit tenant, including user logins, configuration changes, and API token usage. Use these prompts for security monitoring or troubleshooting.

```text
Provide a summary report of authentication activity audit events over the last hour.
```

## Authorization events

Authorization events capture access request decisions between client and server workloads, including approvals, denials, and policy evaluations. Use these prompts to investigate access patterns during a specific time window.

```text
Summarize authorization event activity audit events for the 8am EST hour this morning.
```

## Workload events

Workload events track operational activity for managed workloads, including database queries, HTTP requests, and connection lifecycle events. Use these prompts for daily reporting or incident review.

```text
Show workload event activity audit events over the last day.
```

## Customizing prompts

Adjust these parameters in your prompts to retrieve different data:

* **Time window** - Change “last hour” to “last day”, “past week”, or a specific time range
* **Event type** - Specify authentication, authorization, or workload events
* **Severity** - Filter by severity level (for example, “Show only error-level events”)

### Example variations

```text
Show only error-level authentication events from the past week.
```

```text
List all authorization denials for the last 24 hours.
```

```text
Summarize workload failures between 9am and 12pm EST today.
```

## Learn more

* [Prompt Engineering Best Practices](best-practices.md) - Ten essential techniques for writing effective prompts
* [MCP Server reference](../mcp/mcp-server/reference-mcp-server.md) - Complete tool schemas, filter parameters, and example request/response formats
