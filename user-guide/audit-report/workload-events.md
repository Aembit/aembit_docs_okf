---
type: how-to
title: "How to review Workload Events"
description: "How to review Workload Event information in the Reporting dashboard"
resource: https://docs.aembit.io/user-guide/audit-report/workload-events/
interface: web-ui
tags: [audit-report]
timestamp: 2026-06-17T12:09:19-04:00
type_inferred: true
---

# How to review Workload Events

A workload event is a structured audit record that Aembit generates when application-level traffic flows through Agent Proxy. Workload events capture requests and responses between Client Workloads and Server Workloads across supported application protocols.

These events provide an audit trail for monitoring communication and interactions of workloads within your environment.

## Supported protocols

[Section titled “Supported protocols”](#supported-protocols)

Agent Proxy supports these protocols for workload events:

| Protocol        | Event types                                         | Description                                                                                           |
| --------------- | --------------------------------------------------- | ----------------------------------------------------------------------------------------------------- |
| PostgreSQL      | `postgres.request`, `postgres.response`             | Database queries and responses, including authentication, query operations, and copy operations       |
| Redshift        | `redshift.request`, `redshift.response`             | Same event structure as PostgreSQL                                                                    |
| MySQL / MariaDB | `mysql.request`, `mysql.response`                   | Database queries and responses, including connection handshake, command operations, and error packets |
| Redis           | `redis.request`, `redis.response`                   | Authentication commands and success/error responses                                                   |
| HTTP            | `http.request`, `http.response`                     | HTTP method, URI, headers, and response codes                                                         |
| Snowflake       | `snowflake.request`, `snowflake.response`           | HTTP-based queries to Snowflake endpoints                                                             |
| Oracle Database | `oracleDatabase.request`, `oracleDatabase.response` | Database connection and query events                                                                  |
| MCP             | `mcp.request`, `mcp.response`                       | Model Context Protocol requests and responses                                                         |
| TCP Passthrough | `tcp.open`, `tcp.close`                             | Connection-level events when traffic doesn’t match a supported application protocol handler           |

## Event structure

[Section titled “Event structure”](#event-structure)

Every workload event shares a common JSON structure:

* **version** - Workload event schema version.
* **meta** - Event metadata including timestamp, event type, connection and event identifiers, policy ID, action, and severity. Severity values are `Error`, `Warning`, or `Info`.
* **workload** - The identified Client Workload and Server Workload involved in the event.
* **network** - Network-level details including client and server IP addresses, ports, transport protocol, and proxy port.
* **outcome** - The result of the event: `Passthrough`, `Modified`, or `Error`, with an optional reason.
* **application** - Protocol-specific data that varies by protocol type.

MCP workload events

MCP workload events include an additional top-level field, `authorizationChain`. It contains an ordered list of authorization-event context IDs that tie the event to the authorization decisions behind the request. See [Trace a request with the authorization chain](../troubleshooting/mcp-ai-iam.md#trace-a-request-with-the-authorization-chain).

This field requires version 1.32 or later. Events from earlier components don’t include it.

The following example shows the shape of a workload event:

```json
{
  "version": "1.0.0",
  "meta": {
    "timestamp": "2026-03-15T14:22:08.000Z",
    "eventType": "protocol.direction",
    "connectionId": "3f8b2e1-7c4d-4a91-b5e6-9d2f1c8a4b73",
    "eventId": "e7a4c9d2-1b3f-4e8a-a6c5-2d9b7f3e1a84",
    "policyId": "dd987f8c-34fb-43e2-9d43-89d862e6b7ec",
    "action": "forward",
    "severity": "Info",
    "resourceSetId": "ffffffff-ffff-ffff-ffff-ffffffffffff"
  },
  "workload": {
    "client": {
      "id": "7c466803-9dd4-4388-9e45-420c57a0432c",
      "name": "MCP Client App"
    },
    "server": {
      "id": "49183921-55ab-4856-a8fc-a032af695e0d",
      "name": "MCP Remote Server"
    }
  },
  "network": {
    "clientWorkloadIP": "10.0.1.15",
    "clientWorkloadPort": 53134,
    "transportProtocol": "TCP",
    "serverWorkloadHost": "mcp.example.com",
    "serverWorkloadIP": "10.0.2.30",
    "serverWorkloadPort": 443,
    "proxyPort": 8080
  },
  "outcome": {
    "result": "Passthrough"
  },
  "application": {
    // Protocol-specific fields
  }
}
```

## Types of workload events

[Section titled “Types of workload events”](#types-of-workload-events)

Workload events can be one of two types:

* [Workload request events](#workload-request-events) - when a Client Workload sends a request through Agent Proxy.
* [Workload response events](#workload-response-events) - when the Server Workload returns a response.

### Workload request events

[Section titled “Workload request events”](#workload-request-events)

A workload request event captures the details of a request sent from a Client Workload to a Server Workload through Agent Proxy.

* The `eventType` field follows the pattern `protocol.request` (for example, `mcp.request`).
* The `application` block contains protocol-specific fields. In this example, those fields are `httpMethod`, `httpVersion`, `uri`, and `headers`.

Modified outcome

The `outcome` `result` of `Modified` in this example indicates that Agent Proxy made a change to the request. For example, it injected credentials into the request before forwarding it to the Server Workload. See [Outcome classification](#outcome-classification) for all possible values.

```json
{
  "version": "1.0.0",
  "meta": {
    "timestamp": "2026-03-15T14:22:08.000Z",
    "eventType": "mcp.request",
    "connectionId": "3f8b2e1-7c4d-4a91-b5e6-9d2f1c8a4b73",
    "eventId": "e7a4c9d2-1b3f-4e8a-a6c5-2d9b7f3e1a84",
    "policyId": "dd987f8c-34fb-43e2-9d43-89d862e6b7ec",
    "action": "forward",
    "severity": "Info",
    "resourceSetId": "ffffffff-ffff-ffff-ffff-ffffffffffff"
  },
  "workload": {
    "client": {
      "id": "7c466803-9dd4-4388-9e45-420c57a0432c",
      "name": "MCP Client App"
    },
    "server": {
      "id": "49183921-55ab-4856-a8fc-a032af695e0d",
      "name": "MCP Remote Server"
    }
  },
  "network": {
    "clientWorkloadIP": "10.0.1.15",
    "clientWorkloadPort": 53134,
    "transportProtocol": "TCP",
    "serverWorkloadHost": "mcp.example.com",
    "serverWorkloadIP": "10.0.2.30",
    "serverWorkloadPort": 443,
    "proxyPort": 8080
  },
  "outcome": {
    "result": "Modified"
  },
  "application": {
    "mcp": {
      "httpMethod": "POST",
      "httpVersion": "HTTP/1.1",
      "uri": "/mcp/v1/tools/list",
      "headers": [
        {"content-type": "application/json"},
        {"host": "mcp.example.com"}
      ]
    }
  }
}
```

### Workload response events

[Section titled “Workload response events”](#workload-response-events)

A workload response event captures the details of a response returned from a Server Workload to a Client Workload through Agent Proxy.

* The `eventType` field follows the pattern `protocol.response` (for example, `mcp.response`).
* The `application` block includes protocol-specific fields. In this example, those fields are `httpVersion`, `headers`, and `httpResponseCode`.

Sensitive data protection

For HTTP-based protocols, sensitive headers such as `Authorization` and `Set-Cookie` are automatically redacted from workload event data.

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
    "client": {
      "id": "7c466803-9dd4-4388-9e45-420c57a0432c",
      "name": "MCP Client App"
    },
    "server": {
      "id": "49183921-55ab-4856-a8fc-a032af695e0d",
      "name": "MCP Remote Server"
    }
  },
  "network": {
    "clientWorkloadIP": "10.0.1.15",
    "clientWorkloadPort": 53134,
    "transportProtocol": "TCP",
    "serverWorkloadHost": "mcp.example.com",
    "serverWorkloadIP": "10.0.2.30",
    "serverWorkloadPort": 443,
    "proxyPort": 8080
  },
  "outcome": {
    "result": "Passthrough"
  },
  "application": {
    "mcp": {
      "httpVersion": "HTTP/1.1",
      "headers": [
        {"content-type": "application/json"}
      ],
      "httpResponseCode": 200
    }
  }
}
```

## Outcome classification

[Section titled “Outcome classification”](#outcome-classification)

The `result` field in every workload event indicates how Agent Proxy handled the traffic:

| Outcome       | Description                                                                          |
| ------------- | ------------------------------------------------------------------------------------ |
| `Passthrough` | The request or response passed through Agent Proxy unchanged.                        |
| `Modified`    | Agent Proxy modified the request or response. For example, by injecting credentials. |
| `Error`       | Agent Proxy generated an error response to the Client Workload.                      |

When there is an error, the `reason` field describes what went wrong.

```json
  // ...
  "outcome": {
    "result": "Error",
    "reason": "Unexpected content type from upstream"
  },
  // ...
```

### Error priority

[Section titled “Error priority”](#error-priority)

When multiple error sources exist for a single event, the `reason` field contains the error message from the highest-priority source:

1. **Agent-generated errors** (highest priority) - Errors generated by Agent Proxy itself.
2. **Directive errors** - Errors specified in the directive from the control plane.
3. **Server Workload errors** (lowest priority) - Errors returned by the Server Workload.

## View workload events

[Section titled “View workload events”](#view-workload-events)

To see detailed information about workload events, follow these steps:

1. Log into your Aembit Tenant.

2. Click **Reporting** in the left sidebar.

3. Click the **Workload Events** tab at the top of the Reporting Dashboard.

4. Click the filter icon to apply any filters or the refresh icon to retrieve the latest events.

The following filters are available:

| Filter                           | Description                                                                                                                                               |
| -------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Timespan**                     | The period of time you would like to have event data displayed.                                                                                           |
| **Application Protocols**        | The application protocol type to filter by.                                                                                                               |
| **Severity**                     | Filter by event severity: `Error`, `Warning`, or `Info`.                                                                                                  |
| **Server Workload Name**         | Filter by a specific Server Workload.                                                                                                                     |
| **Client Workload Name**         | Filter by a specific Client Workload.                                                                                                                     |
| **User (MCP App Protocol only)** | Filter MCP Workload Events to a specific user. Available only when filtering by `Application Protocol = MCP`. Matches the `application.mcp.userId` field. |

Event timing

Workload Events appear in near-real-time, but not instantly. After traffic flows through Agent Proxy, allow a short time for new events to show up in the dashboard.

### Event details

[Section titled “Event details”](#event-details)

When you select a workload event from the dashboard, you can expand the view to display detailed data for that event.

![The Workload Events reporting view with an expanded event showing a summary panel on the left and full JSON output on the right](https://docs.aembit.io/_astro/reporting-workload-event-detail.BeuaZ7Wc_1kXO4S.webp)

The expanded view displays the event metadata, workload identification, network details, outcome, and protocol-specific application data as described in [Event structure](#event-structure).
