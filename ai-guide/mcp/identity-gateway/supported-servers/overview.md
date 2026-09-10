---
type: reference
title: "Supported MCP Servers"
description: "Configuration guides for connecting third-party MCP servers to AI agents through the Aembit MCP Identity Gateway."
resource: https://docs.aembit.io/ai-guide/mcp/identity-gateway/supported-servers/
interface: mcp
tags: ["supported-server", "identity-gateway", "mcp"]
timestamp: 2026-08-11T10:49:58-07:00
---

# Supported MCP Servers

The Aembit MCP Identity Gateway brokers AI agent access to third-party MCP servers. It enforces Access Policies and injects per-user credentials, so agents never hold credentials directly.

Each of the following guides configures one MCP server to work through the Identity Gateway, using the **MCP User-Based Access Token** credential type.

> **MCP access vs. direct API access**
>
> These guides configure **MCP tool access through the Identity Gateway**. For direct REST API access to the same vendor (a traditional Server Workload), see the [Server Workload guides](../../../../user-guide/access-policies/server-workloads/guides/overview.md).

## Supported servers

Configuration guides are available for the following servers:

### CRM

* [Salesforce](salesforce.md)

### Cloud

* [Microsoft Enterprise MCP](microsoft-enterprise.md)

### Data analytics

* [BigQuery](bigquery.md)
* [Databricks](databricks.md)

### Developer tools

* [GitHub](github.md)
* [Atlassian](atlassian.md)

### Financial

* [Stripe](stripe.md)
* [FactSet](factset.md)
* [Kensho](kensho.md)

### Productivity

* [Slack](slack.md)
* [Notion](notion.md)
* [Google Workspace](google-workspace.md): Drive, Calendar, and People
* [Office 365](office365.md)

### Security

* [Wiz](wiz.md)
