---
type: reference
title: "Aembit MCP Server reference"
description: "Technical reference for the Aembit Model Context Protocol (MCP) Server including endpoints, authentication, tools, and resources."
resource: https://docs.aembit.io/ai-guide/mcp/mcp-server/reference-mcp-server/
interface: mcp
tags: ["mcp-server", "mcp"]
timestamp: 2026-05-12T15:40:45-07:00
---

# Aembit MCP Server reference

This page documents the Aembit MCP Server API, including the service URL, authentication headers, available tools, and metadata resources. For a conceptual overview, see [About the MCP Server](about-mcp-server.md).

> **Read-only access**
>
> The Aembit MCP Server only supports read operations. You can’t create, update, or delete data through the MCP Server.

## MCP service URL

Use the following URL pattern to connect to the Aembit MCP Server:

* **Production:**

  ```shell
  https://<tenantId>.mcp.useast2.aembit.io/mcp
  ```

Replace `<tenantId>` with your tenant identifier, visible in the Aembit Admin UI Profile screen.

## Authentication

All requests require bearer token authentication using your Aembit API Token.

| Header                 | Required | Description                                                                      |
| ---------------------- | -------- | -------------------------------------------------------------------------------- |
| `Authorization`        | Required | Bearer token for all requests.                                                   |
| `X-Aembit-ResourceSet` | Optional | Scope queries to a specific resource set. Omit to use your default resource set. |

**Authorization:**

```shell
Authorization: Bearer <apiToken>
```

**Resource set scoping:**

```shell
X-Aembit-ResourceSet: <resourceSetId>
```

## Tools

The following tools are the available MCP Server tools for querying Aembit event logs:

### `get_audit_logs`

See also [Audit Logs](../../../user-guide/audit-report/audit-logs.md)

Retrieve audit logs with filtering and pagination. By default, returns the last 30 days of audit logs, sorted by newest first.

**Parameters**

All parameters are optional.

| Parameter         | Type           | Default       | Description                                                                                                                                      |
| ----------------- | -------------- | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| `page`            | integer        | `1`           | Page number for pagination.                                                                                                                      |
| `perPage`         | integer        | `100`         | Results per page. Maximum and default value is `100`.                                                                                            |
| `orderBy`         | string         | `"CreatedAt"` | Sort field. Valid values: `CreatedAt`, `Category`, `ActorDisplayName`, `Activity`, `Target`, `OutcomeResult`, `Severity`.                        |
| `descending`      | boolean        | `true`        | Sort in descending order when `true`.                                                                                                            |
| `search`          | string         | `""`          | Search text to filter audit logs by content.                                                                                                     |
| `startDate`       | string \| null | `null`        | Start of date range in ISO 8601 format. Requires `endDate`. Overrides `spanLastMinutes` and `spanLastDays`.                                      |
| `endDate`         | string \| null | `null`        | End of date range in ISO 8601 format. Requires `startDate`. The server clamps values beyond the current Coordinated Universal Time (UTC) to now. |
| `spanLastMinutes` | integer        | `0`           | Return logs from the last N minutes. Takes priority over `spanLastDays`. Ignored when you specify `startDate`/`endDate`.                         |
| `spanLastDays`    | integer        | `30`          | Return logs from the last N days. Only applies when you don’t specify `startDate`/`endDate` or `spanLastMinutes`.                                |
| `category`        | string \| null | `null`        | Filter by audit log category. See [Audit log categories](#audit-log-categories) for valid values.                                                |
| `severity`        | string \| null | `null`        | Filter by severity. Valid values: `Info`, `Warn`, `Alert`.                                                                                       |

**Date range priority:** `startDate`/`endDate` > `spanLastMinutes` > `spanLastDays`.

*Example request*:

```json
{
  "category": "Authentication",
  "severity": "Warn",
  "spanLastDays": 7,
  "perPage": 10
}
```

***

### `get_auth_events`

See also [Audit and Report](../../../user-guide/audit-report/overview.md)

Retrieve authorization events with filtering and pagination. By default, returns the last 24 hours of authorization events, sorted by newest first.

**Parameters**

All parameters are optional.

| Parameter         | Type           | Default       | Description                                                                                                                 |
| ----------------- | -------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------- |
| `page`            | integer        | `1`           | Page number for pagination.                                                                                                 |
| `perPage`         | integer        | `100`         | Results per page. Maximum and default value is `100`.                                                                       |
| `orderBy`         | string         | `"Timestamp"` | Sort field. Valid values: `Timestamp`, `ClientIp`, `ContextId`, `ClientWorkload`, `ServerWorkload`, `Severity`.             |
| `descending`      | boolean        | `true`        | Sort in descending order when `true`.                                                                                       |
| `search`          | string         | `""`          | Search text to filter authorization events by content.                                                                      |
| `startDate`       | string \| null | `null`        | Start of date range in ISO 8601 format. Requires `endDate`. Overrides `spanLastMinutes` and `spanLastHours`.                |
| `endDate`         | string \| null | `null`        | End of date range in ISO 8601 format. Requires `startDate`. The server clamps values beyond the current UTC to now.         |
| `spanLastMinutes` | integer        | `0`           | Return events from the last N minutes. Takes priority over `spanLastHours`. Ignored when you specify `startDate`/`endDate`. |
| `spanLastHours`   | integer        | `24`          | Return events from the last N hours. Only applies when you don’t specify `startDate`/`endDate` or `spanLastMinutes`.        |
| `severity`        | string \| null | `null`        | Filter by severity. Valid values: `Error`, `Alert`, `Warn`, `Info`.                                                         |
| `eventType`       | string \| null | `null`        | Filter by event type. Valid values: `Request`, `Authorization`, `Credential`.                                               |

**Date range priority:** `startDate`/`endDate` > `spanLastMinutes` > `spanLastHours`.

*Example request*:

```json
{
  "eventType": "Request",
  "severity": "Error",
  "startDate": "2025-12-02T08:00:00Z",
  "endDate": "2025-12-02T09:00:00Z",
  "perPage": 50
}
```

***

### `get_workload_events`

See also [Workload Events](../../../user-guide/audit-report/workload-events/overview.md)

Retrieve workload events with filtering and pagination. By default, returns the last 24 hours of workload events, sorted by newest first.

**Parameters**

All parameters are optional.

| Parameter         | Type                     | Default       | Description                                                                                                                 |
| ----------------- | ------------------------ | ------------- | --------------------------------------------------------------------------------------------------------------------------- |
| `page`            | integer                  | `1`           | Page number for pagination.                                                                                                 |
| `perPage`         | integer                  | `100`         | Results per page. Maximum and default value is `100`.                                                                       |
| `orderBy`         | string                   | `"Timestamp"` | Sort field. Valid values: `Timestamp`, `ConnectionId`, `EventType`, `ClientWorkload`, `ServerWorkload`, `Severity`.         |
| `descending`      | boolean                  | `true`        | Sort in descending order when `true`.                                                                                       |
| `search`          | string                   | `""`          | Search text to filter workload events by content.                                                                           |
| `startDate`       | string \| null           | `null`        | Start of date range in ISO 8601 format. Requires `endDate`. Overrides `spanLastMinutes` and `spanLastHours`.                |
| `endDate`         | string \| null           | `null`        | End of date range in ISO 8601 format. Requires `startDate`. The server clamps values beyond the current UTC to now.         |
| `spanLastMinutes` | integer                  | `0`           | Return events from the last N minutes. Takes priority over `spanLastHours`. Ignored when you specify `startDate`/`endDate`. |
| `spanLastHours`   | integer                  | `24`          | Return events from the last N hours. Only applies when you don’t specify `startDate`/`endDate` or `spanLastMinutes`.        |
| `severity`        | string \| null           | `null`        | Filter by severity. Valid values: `Error`, `Alert`, `Warn`, `Info`.                                                         |
| `appProtocol`     | string \| null           | `null`        | Filter by application protocol. See [Workload event types](#workload-event-types) for valid values.                         |
| `sourceWorkload`  | array of `UUIDs` \| null | `null`        | Filter by client workload IDs.                                                                                              |
| `targetWorkload`  | array of `UUIDs` \| null | `null`        | Filter by server workload IDs.                                                                                              |

**Date range priority:** `startDate`/`endDate` > `spanLastMinutes` > `spanLastHours`.

*Example request*:

```json
{
  "appProtocol": "Postgres",
  "severity": "Error",
  "spanLastHours": 6
}
```

***

## Resources

The MCP Server exposes the following resources as machine-readable metadata. AI agents use these resources to discover valid filter values for each tool.

### Audit log severities

Severity values for the `severity` filter in `get_audit_logs`.

| Value   | Description                                                         |
| ------- | ------------------------------------------------------------------- |
| `Info`  | Routine administrative activity.                                    |
| `Warn`  | Events that may indicate a configuration issue or unusual activity. |
| `Alert` | High-importance events requiring attention.                         |

### Audit log categories

Valid values for the `category` filter in `get_audit_logs`. Each category corresponds to a type of activity logged in the Aembit Admin UI. See [Audit Logs](../../../user-guide/audit-report/audit-logs.md) for descriptions of each category.

`Unknown`, `Tenant`, `Users`, `Authentication`, `Workloads`, `AccessPolicies`, `Agents`, `CredentialProvider`, `TrustProvider`, `AgentControllers`, `LogStreams`, `Integrations`, `AccessConditions`, `Roles`, `IdentityProviders`, `ResourceSets`, `Analysis`, `Routing`, `SignOnPolicies`, `PkiSettings`, `StandaloneCertificateAuthorities`, `CredentialProviderIntegrations`, `DiscoveryIntegration`, `GlobalPolicyCompliance`

### Authorization event types

Valid values for the `eventType` filter in `get_auth_events`. Each type represents a step in Aembit’s access authorization flow. See [Access Authorization Events](../../../user-guide/audit-report/access-authorization-events.md) for the full event schema.

| Value           | Description                                                                                             |
| --------------- | ------------------------------------------------------------------------------------------------------- |
| `Request`       | Captures the incoming access request and associated metadata.                                           |
| `Authorization` | Records the outcome of Access Policy evaluation, including Trust Provider and Access Condition results. |
| `Credential`    | Records the result of credential retrieval from the Credential Provider.                                |

### Authorization event severities

Severity values for the `severity` filter in `get_auth_events`.

| Value   | Description                                            |
| ------- | ------------------------------------------------------ |
| `Error` | Authorization failed due to an internal error.         |
| `Alert` | High-priority authorization event requiring attention. |
| `Warn`  | Concerning authorization activity.                     |
| `Info`  | Standard, expected authorization activity.             |

### Workload event types

Application protocol values used with the `appProtocol` filter parameter:

`Redshift`, `HTTP`, `MySQL`, `Postgres`, `Redis`, `Snowflake`, `TCP`, `OracleDatabase`, `MCP`

### Workload event severities

Severity values for the `severity` filter in `get_workload_events`.

| Value   | Description                                         |
| ------- | --------------------------------------------------- |
| `Error` | Workload connection failed or encountered an error. |
| `Alert` | High-priority workload event requiring attention.   |
| `Warn`  | Concerning workload activity.                       |
| `Info`  | Standard, expected workload activity.               |

## Supported integration tools

The following tools work with the Aembit MCP Server:

* [MCP Inspector](https://www.mcpjam.com/)
* [modelcontextprotocol/inspector (GitHub)](https://github.com/modelcontextprotocol/inspector)
* [Claude Code CLI](https://code.claude.com/docs/en/mcp)
* [GitHub Copilot Integration](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/coding-agent/extend-coding-agent-with-mcp)
* [Visual Studio](https://learn.microsoft.com/en-us/visualstudio/ide/mcp-servers?view=visualstudio)

## Sample prompts

For example prompts and prompt engineering best practices, see the [Prompt Library](../../prompt-library/overview.md).

## Drilldown and filtering

* **Drilldown:** Tools and integrations such as MCP Inspector allow you to drill into event details for each result.
* **Filtering:** Each tool accepts optional filter parameters for time range, severity, and tool-specific fields. See the preceding parameter tables for available filters per tool.

## Audit logging

Aembit logs all queries to the MCP Server in the audit trail, including the identity of the requesting agent.

## Rate limits and constraints

The MCP Server doesn’t enforce application-level rate limiting. The maximum page size (`perPage`) is `100`. Requests that exceed this value cap to `100`.

## Limitations

> **Read-only access**
>
> The Aembit MCP Server only supports read operations. You can’t create, update, or delete data through the MCP Server.

* Only the tools and resources listed in the preceding sections are available.
* Your Aembit administrator must enable the MCP Server for your tenant.
