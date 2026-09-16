---
type: reference
title: "Supported protocols and application fields"
description: "The application fields Agent Proxy records in Workload Events for each supported protocol"
resource: https://docs.aembit.io/user-guide/audit-report/workload-events/supported-protocols/
interface: web-ui
tags: ["workload-event", "audit-report"]
timestamp: 2026-09-16T12:05:53-04:00
---

# Supported protocols and application fields

Agent Proxy generates workload events for the following protocols and technologies:

* HTTP
* Snowflake
* MCP
* PostgreSQL
* Amazon Redshift
* MySQL and MariaDB
* Oracle Database
* Redis
* TCP Passthrough

This page lists the `application` fields Agent Proxy records for each protocol, split by request and response event. For the common fields that every event shares, see the [Workload Event reference](reference.md). For a conceptual overview, see [Workload Events](overview.md).

The **Presence** column indicates whether Agent Proxy always includes the field (**Always**) or includes it only under certain conditions (**Optional**).

## HTTP and Snowflake

HTTP fields appear under `application.http`. Snowflake events are HTTP-based queries to Snowflake endpoints and use the same fields, under `application.snowflake`.

Request fields:

| Field         | Presence | Description                                                                                  |
| ------------- | -------- | -------------------------------------------------------------------------------------------- |
| `httpMethod`  | Always   | HTTP method, such as `GET` or `POST`.                                                        |
| `httpVersion` | Always   | HTTP version, such as `HTTP/1.1`.                                                            |
| `uri`         | Always   | Requested URI, with the query string removed to avoid recording sensitive values.            |
| `headers`     | Always   | A list of single-key objects, each a header name and value, with sensitive headers redacted. |

Response fields:

| Field              | Presence | Description                                                                                  |
| ------------------ | -------- | -------------------------------------------------------------------------------------------- |
| `httpVersion`      | Always   | HTTP version, such as `HTTP/1.1`.                                                            |
| `headers`          | Always   | A list of single-key objects, each a header name and value, with sensitive headers redacted. |
| `httpResponseCode` | Always   | HTTP response status code, such as `200` or `404`.                                           |

Example `application.http` block

```json
"application": {
  "http": {
    "httpMethod": "GET",
    "httpVersion": "HTTP/1.1",
    "uri": "/v2/accounts",
    "headers": [
      {"content-type": "application/json"},
      {"host": "api.example.com"}
    ],
    "httpResponseCode": 200
  }
}
```

## MCP

MCP events use the HTTP-based fields under `application.mcp`, plus MCP-specific fields.

Request fields:

| Field                | Presence | Description                                                                                                          |
| -------------------- | -------- | -------------------------------------------------------------------------------------------------------------------- |
| `httpMethod`         | Always   | HTTP method, such as `POST`.                                                                                         |
| `httpVersion`        | Always   | HTTP version, such as `HTTP/1.1`.                                                                                    |
| `uri`                | Always   | Requested URI, with the query string removed to avoid recording sensitive values.                                    |
| `headers`            | Always   | A list of single-key objects, each a header name and value, with sensitive headers redacted.                         |
| `mcpMethod`          | Always   | The MCP method for the request, such as `tools/list` or `tools/call`.                                                |
| `mcpSessionId`       | Always   | The MCP session ID, shared across requests and responses in the same session.                                        |
| `mcpProtocolVersion` | Optional | The negotiated MCP protocol version, when available.                                                                 |
| `mcpServerName`      | Optional | The name of the target MCP server, when available.                                                                   |
| `mcpToolName`        | Optional | The tool invoked, on `tools/call` requests.                                                                          |
| `mcpRequestId`       | Optional | The MCP request identifier, used to correlate a response with its request.                                           |
| `userId`             | Optional | The user identity for MCP flows that involve a human identity, matching the **User (MCP App Protocol only)** filter. |

Response fields:

| Field                | Presence | Description                                                                                  |
| -------------------- | -------- | -------------------------------------------------------------------------------------------- |
| `httpVersion`        | Always   | HTTP version, such as `HTTP/1.1`.                                                            |
| `headers`            | Always   | A list of single-key objects, each a header name and value, with sensitive headers redacted. |
| `httpResponseCode`   | Always   | HTTP response status code, such as `200` or `404`.                                           |
| `mcpMethod`          | Always   | The MCP method the response corresponds to, such as `tools/list` or `tools/call`.            |
| `mcpSessionId`       | Always   | The MCP session ID, shared across requests and responses in the same session.                |
| `mcpProtocolVersion` | Optional | The negotiated MCP protocol version, when available.                                         |
| `mcpServerName`      | Optional | The name of the target MCP server, when available.                                           |
| `mcpRequestId`       | Optional | The MCP request identifier, used to correlate the response with its request.                 |

Example `application.mcp` block

```json
"application": {
  "mcp": {
    "httpMethod": "POST",
    "httpVersion": "HTTP/1.1",
    "uri": "/mcp/v1/tools/call",
    "headers": [
      {"content-type": "application/json"},
      {"host": "mcp.example.com"}
    ],
    "mcpMethod": "tools/call",
    "mcpSessionId": "3d9c1e77-5b42-4c8a-9f16-2a7e8b0d4c31",
    "mcpProtocolVersion": "2025-06-18",
    "mcpServerName": "example-mcp-server",
    "mcpToolName": "search_records",
    "mcpRequestId": "42",
    "userId": "alice@example.com"
  }
}
```

### Content Security

When the Access Policy that governs MCP Identity Gateway traffic includes a Content Security Provider, MCP workload events record the decision under `application.mcp.contentSecurity`. Each provider writes to its own field inside that element, so the field name identifies which provider acted.

#### CrowdStrike AIDR

For the CrowdStrike AIDR provider, the `crowdStrikeAidr` object contains these fields:

* **`decision`** - The decision CrowdStrike AIDR returned for the inspected content.
* **`requestId`** - CrowdStrike’s identifier for the inspection request. Use it to correlate the event with the corresponding finding in the CrowdStrike console.
* **`reason`** - The reason for a blocked decision (for example, `Blocked by AIDR policy`).

The event’s `outcome.result` describes how the MCP Identity Gateway handled the message overall, so it doesn’t map one-to-one to the AIDR decision:

* `Passthrough` with `decision: allowed` - AIDR allowed a response (a tool listing or tool result), and the MCP Identity Gateway relayed it to the client unchanged.
* `Modified` with `decision: allowed` - AIDR allowed a request, but the MCP Identity Gateway still rewrote it to inject credentials toward the MCP server. Because credential injection applies to most proxied requests, allowed requests typically show `Modified`, not `Passthrough`.
* `Modified` with `decision: transformed` - AIDR redacted the content, and the MCP Identity Gateway forwarded the transformed version.
* `Error` with `decision: blocked` - AIDR blocked the content, and the MCP Identity Gateway sent the client an error response in its place. The `crowdStrikeAidr.reason` field carries the block reason.
* `Error` with no `decision` - CrowdStrike AIDR returned an error or was unreachable, and the MCP Identity Gateway blocked the content (fail closed). The event’s `reason` field carries the error detail.

Workload events record metadata about the decision, never the MCP request or response content that AIDR evaluated.

Example `mcp.response` event with a transformed tool result

```json
{
  "version": "1.0.0",
  "meta": {
    "timestamp": "2026-03-15T14:22:08.123Z",
    "eventType": "mcp.response",
    "connectionId": "3f8b2e1-7c4d-4a91-b5e6-9d2f1c8a4b73",
    "eventId": "b2c8d4e6-3a1f-4b7c-9d5e-8f2a6c4b1d73",
    "policyId": "dd987f8c-34fb-43e2-9d43-89d862e6b7ec",
    "action": "forward",
    "severity": "Info",
    "resourceSetId": "ffffffff-ffff-ffff-ffff-ffffffffffff"
  },
  "workload": {
    "client": { "id": "7c466803-9dd4-4388-9e45-420c57a0432c", "name": "MCP Identity Gateway" },
    "server": { "id": "49183921-55ab-4856-a8fc-a032af695e0d", "name": "MCP Remote Server" }
  },
  "outcome": {
    "result": "Modified"
  },
  "application": {
    "mcp": {
      "contentSecurity": {
        "crowdStrikeAidr": {
          "decision": "transformed",
          "requestId": "prq_example1234567890abcdefghijklmn"
        }
      },
      "httpResponseCode": 200,
      "httpVersion": "HTTP/1.1",
      "mcpMethod": "tools/call",
      "mcpProtocolVersion": "2025-11-25",
      "mcpRequestId": 1,
      "mcpServerName": "MCP Remote Server",
      "mcpSessionId": "eec9c934-55b4-4797-9993-36dfa4838399",
      "mcpToolName": "example_tool",
      "userId": "user@example.com"
    }
  },
  "authorizationChain": [
    "7fcd3dcc-dc4a-4e35-94e0-76221e12b83a",
    "64d74e7a-19aa-4231-8286-5687b8959ecd",
    "4b8be4ae-aa16-4003-965b-18fd7fab390b"
  ]
}
```

> **“Report” is CrowdStrike-side**
>
> Aembit enforces and records allow, block, and transform decisions. “Report,” or report-only, is an enforcement mode configured in CrowdStrike AIDR. Aembit receives no notification of report findings and generates no event data for them. Reporting in Aembit means event tracking and logging, such as the workload events on this page.

#### Correlate events with CrowdStrike AIDR findings

CrowdStrike AIDR keeps its own findings for the content it inspects. The `requestId` field links an Aembit workload event to the matching CrowdStrike record:

1. In your Aembit Tenant, click **Reporting** in the left sidebar, then click the **Workload Events** tab.

2. Filter **Application Protocols** to **MCP**, and optionally filter **User** to narrow the results.

3. Open the event and find `requestId` under `application.mcp.contentSecurity.crowdStrikeAidr`.

4. In the CrowdStrike console, search your AIDR findings for that request ID.

To work in the other direction, start from the request ID and timestamp on a CrowdStrike AIDR finding. Filter Workload Events to the matching timespan and the MCP protocol, then match the `requestId` field.

For how inspection works and how to configure it, see [CrowdStrike AIDR Content Security](../../access-policies/content-security/crowdstrike-aidr/overview.md).

#### MCP Tool Access Control

For the MCP Tool Access Control Content Security Provider, the `aembitMcpToolsAcl` object contains these fields:

| Field           | Presence | Description                                                                               |
| --------------- | -------- | ----------------------------------------------------------------------------------------- |
| `decision`      | Always   | What the control did: `allowed`, `filtered`, or `blocked`.                                |
| `control`       | Always   | Which control decided: `visible` or `invocable`.                                          |
| `matchedRules`  | Calls    | Every rule the MCP tool name matched. Empty when no rule matched and the default decided. |
| `toolsRemoved`  | Listings | The MCP tools dropped from the listing, as the upstream MCP server published them.        |
| `toolsReturned` | Listings | How many MCP tools the listing still carries.                                             |
| `reason`        | Blocked  | Text naming the MCP tool and the rule that matched it.                                    |

A field is absent when it doesn’t apply. A listing evaluates many MCP tools at once, so it reports `toolsRemoved` and `toolsReturned` in place of `matchedRules`.

The event’s [`outcome.result`](reference.md#result) and [`meta.severity`](reference.md#severity) follow the decision:

| `decision` | `control`   | `outcome.result` | `meta.severity` | What happened                                                                              |
| ---------- | ----------- | ---------------- | --------------- | ------------------------------------------------------------------------------------------ |
| `allowed`  | `visible`   | `Passthrough`    | `Info`          | The control evaluated a listing and removed nothing.                                       |
| `allowed`  | `invocable` | `Modified`       | `Info`          | The control allowed a call, and the MCP Identity Gateway rewrote it to inject credentials. |
| `filtered` | `visible`   | `Modified`       | `Info`          | Tool Visibility removed MCP tools from a listing.                                          |
| `blocked`  | `invocable` | `Error`          | `Error`         | Tool Invocation denied a call and returned an error to the AI agent.                       |
| `blocked`  | `visible`   | `Error`          | `Error`         | Tool Visibility failed closed on a listing.                                                |

Example `aembitMcpToolsAcl` object for a blocked tool call

```json
"contentSecurity": {
  "aembitMcpToolsAcl": {
    "decision": "blocked",
    "control": "invocable",
    "matchedRules": ["delete_*"],
    "reason": "Tool 'delete_issue' is not invocable under this policy (matched: delete_*)"
  }
}
```

Example `aembitMcpToolsAcl` object for a filtered tool listing

```json
"contentSecurity": {
  "aembitMcpToolsAcl": {
    "decision": "filtered",
    "control": "visible",
    "toolsReturned": 12,
    "toolsRemoved": ["admin_reset", "drop_table"]
  }
}
```

For the controls and the glob syntax for MCP tool names, see [MCP Tool Access Control](../../access-policies/content-security/mcp-tool-access-control/overview.md).

#### Where MCP Tool Access Control decisions appear

* A failed listing reaches the AI agent as an empty MCP tool list rather than an error, so the event is the only place it shows.
* An invocation decision appears on one event: an allowed call on the request event, and a blocked call on the response event.
* A visibility decision appears only on the response event.
* The MCP Identity Gateway runs the rules for each upstream MCP server it forwards to, so a listing that spans servers reports per server.

## PostgreSQL and Amazon Redshift

PostgreSQL fields appear under `application.postgres`. Amazon Redshift uses the same fields, under `application.redshift`. Request and response events carry the same fields; the value of `message.type` differs by direction.

Request fields:

| Field             | Presence | Description                                    |
| ----------------- | -------- | ---------------------------------------------- |
| `protocolVersion` | Optional | The PostgreSQL wire protocol version.          |
| `user`            | Optional | The database user.                             |
| `database`        | Optional | The target database name.                      |
| `message.type`    | Always   | The protocol message type, such as `Query`.    |
| `tls.version`     | Optional | The negotiated TLS version, such as `TLSv1.2`. |

Response fields:

| Field             | Presence | Description                                    |
| ----------------- | -------- | ---------------------------------------------- |
| `protocolVersion` | Optional | The PostgreSQL wire protocol version.          |
| `user`            | Optional | The database user.                             |
| `database`        | Optional | The target database name.                      |
| `message.type`    | Always   | The protocol message type.                     |
| `tls.version`     | Optional | The negotiated TLS version, such as `TLSv1.2`. |

Example `application.postgres` block

```json
"application": {
  "postgres": {
    "protocolVersion": "3.0",
    "user": "analytics",
    "database": "warehouse",
    "message": {
      "type": "Query"
    },
    "tls": {
      "version": "TLSv1.2"
    }
  }
}
```

## MySQL and MariaDB

MySQL fields appear under `application.mysql`. MariaDB shares the MySQL protocol handler and uses the same event types and fields. Request and response events carry the same fields; the value of `message.type` differs by direction.

> **Note**
>
> MySQL field names use `snake_case`, unlike the `camelCase` of the other protocols.

Request fields:

| Field                   | Presence | Description                                                                                               |
| ----------------------- | -------- | --------------------------------------------------------------------------------------------------------- |
| `protocol_version`      | Always   | The MySQL wire protocol version.                                                                          |
| `server_version`        | Always   | The MySQL server version string, such as `8.4.7`.                                                         |
| `server_thread_id`      | Always   | The server-assigned connection thread ID.                                                                 |
| `user`                  | Always   | The database user.                                                                                        |
| `message.type`          | Always   | The protocol message type, such as `ComQuery`.                                                            |
| `connection_attributes` | Optional | Client-supplied connection metadata, such as client name and version, operating system, and program name. |

Response fields:

| Field                   | Presence | Description                                                                                               |
| ----------------------- | -------- | --------------------------------------------------------------------------------------------------------- |
| `protocol_version`      | Always   | The MySQL wire protocol version.                                                                          |
| `server_version`        | Always   | The MySQL server version string, such as `8.4.7`.                                                         |
| `server_thread_id`      | Always   | The server-assigned connection thread ID.                                                                 |
| `user`                  | Always   | The database user.                                                                                        |
| `message.type`          | Always   | The protocol message type.                                                                                |
| `connection_attributes` | Optional | Client-supplied connection metadata, such as client name and version, operating system, and program name. |

Example `application.mysql` block

```json
"application": {
  "mysql": {
    "protocol_version": "10",
    "server_version": "8.4.7",
    "server_thread_id": 4182,
    "connection_attributes": {
      "_client_name": "libmysql",
      "_client_version": "8.4.7",
      "_os": "Linux",
      "program_name": "mysql"
    },
    "user": "reporting",
    "message": {
      "type": "ComQuery"
    }
  }
}
```

## Oracle Database and Redis

Oracle Database fields appear under `application.oracleDatabase`, and Redis fields under `application.redis`. Both carry a single field, whose value differs between request and response events.

Request fields:

| Field          | Presence | Description                                                                                                                               |
| -------------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| `message.type` | Always   | The protocol message type. For Redis, the command name, such as `AUTH`. For Oracle Database, the packet type, such as `Connect (NSPTCN)`. |

Response fields:

| Field          | Presence | Description                                                                                                                               |
| -------------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| `message.type` | Always   | The protocol message type. For Redis, the status, such as `OK` or `ERR`. For Oracle Database, the packet type, such as `Accept (NSPTAC)`. |

Example `application.oracleDatabase` and `application.redis` blocks

```json
"application": {
  "oracleDatabase": {
    "message": {
      "type": "Connect (NSPTCN)"
    }
  }
}
```

```json
"application": {
  "redis": {
    "message": {
      "type": "AUTH"
    }
  }
}
```

## TCP Passthrough

TCP Passthrough captures traffic that doesn’t match a supported application-protocol handler. Instead of request and response events, Agent Proxy generates the connection-level event types `tcp.open` and `tcp.close`, which don’t include an `application` block. When Agent Proxy can identify the underlying protocol, it records it in `meta.identifiedProtocol`.
