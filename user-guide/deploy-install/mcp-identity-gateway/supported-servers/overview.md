---
type: reference
title: "Supported MCP Servers"
description: "Configuration guides for connecting third-party MCP servers to AI agents through the Aembit MCP Identity Gateway."
resource: https://docs.aembit.io/user-guide/deploy-install/mcp-identity-gateway/supported-servers/
interface: mcp
tags: ["supported-server", "mcp-identity-gateway", "deploy-install"]
timestamp: 2026-11-09T14:11:42-08:00
---

# Supported MCP Servers

The Aembit MCP Identity Gateway brokers AI agent access to third-party MCP servers. It enforces Access Policies and injects credentials, so AI agents never hold credentials directly.

Most of the following guides configure one MCP server with the **MCP User-Based Access Token** credential type, so each user gets their own credentials. The Atlassian guide also covers the **MCP Enterprise Managed Access Token** credential type, which the Atlassian MCP server supports. The CrowdStrike Falcon guide uses the **API Key** credential type instead, because the Falcon MCP server accepts one static API key that every user shares.

> **MCP access vs. direct API access**
>
> These guides configure **MCP tool access through MCP Identity Gateway**. For direct REST API access to the same vendor (a traditional Server Workload), see the [Server Workload guides](../../../access-policies/server-workloads/guides/overview.md).

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
* [GitLab](gitlab.md)
* [Datadog](datadog.md)

### Financial

* [Stripe](stripe.md)
* [Kensho](kensho.md)

### Productivity

* [Slack](slack.md)
* [Notion](notion.md)
* [Google Workspace](google-workspace.md): Drive, Calendar, People, and Chat
* [Office 365](office365.md)
* [Box](box.md)

### Security

* [Wiz](wiz.md)
* [CrowdStrike Falcon](crowdstrike.md)
