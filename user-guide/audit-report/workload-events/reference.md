---
type: reference
title: "Workload Event reference"
description: "The common fields that every Workload Event shares, with examples"
resource: https://docs.aembit.io/user-guide/audit-report/workload-events/reference/
interface: web-ui
tags: ["workload-event", "audit-report"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Workload Event reference

This reference describes the common fields that every workload event shares. For the protocol-specific `application` fields, see [Supported protocols and application fields](supported-protocols.md). For a conceptual overview and annotated examples, see [Workload Events](overview.md). To view events in the dashboard, see [Review Workload Events](review.md).

The **Presence** column indicates whether Agent Proxy always includes the field (**Always**) or includes it only under certain conditions (**Optional**).

## JSON structure

Every workload event JSON response or request shares a common structure of top-level, `meta`, `workload`, `network`, and `outcome` fields. The `application` field carries [protocol-specific fields](supported-protocols.md).

### Top-level fields

| Field                | Presence | Description                                                                                                                                                                                                                                                                                                                                                                                                              |
| -------------------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `version`            | Always   | The workload event schema version, such as `1.0.0`.                                                                                                                                                                                                                                                                                                                                                                      |
| `authorizationChain` | Optional | An ordered list of authorization-event context IDs. MCP events include it from version 1.32 on. Agent Proxy events include it from version 1.34.5755 on, where the chain holds the IDs of the directive retrieval and the credential retrieval that authorized the connection. See [Trace a request with the authorization chain](../../troubleshooting/mcp-ai-iam.md#trace-a-request-with-the-authorization-chain). |

Example top-level fields

```json
{
  "version": "1.0.0",
  "authorizationChain": [
    "b7e2c1a4-3f8d-4e6b-9a1c-2d5f7e0b8c34"
  ]
  // meta, workload, network, outcome, and application follow
}
```

### `meta`

| Field                | Presence | Description                                                                                            |
| -------------------- | -------- | ------------------------------------------------------------------------------------------------------ |
| `timestamp`          | Always   | The time the event occurred, in Coordinated Universal Time (UTC).                                      |
| `eventType`          | Always   | The event type, in the form `<protocol>.<direction>`, such as `mcp.request` or `postgres.response`.    |
| `connectionId`       | Always   | The identifier of the connection the event belongs to.                                                 |
| `eventId`            | Always   | The unique identifier of the event.                                                                    |
| `action`             | Always   | The action Agent Proxy took. Always `forward`.                                                         |
| `severity`           | Always   | The event severity: `Error`, `Warning`, or `Info`. See [`severity`](#severity).                        |
| `policyId`           | Always   | The identifier of the Access Policy applied to the traffic, or `null` when no policy applied.          |
| `resourceSetId`      | Optional | The identifier of the Resource Set the workloads belong to. Present when available.                    |
| `identifiedProtocol` | Optional | For TCP Passthrough events, the underlying protocol Agent Proxy identified, when it can determine one. |

Example `meta` block

```json
"meta": {
  "timestamp": "2026-03-15T14:22:08.000Z",
  "eventType": "mcp.request",
  "connectionId": "3f8b2e1-7c4d-4a91-b5e6-9d2f1c8a4b73",
  "eventId": "e7a4c9d2-1b3f-4e8a-a6c5-2d9b7f3e1a84",
  "policyId": "dd987f8c-34fb-43e2-9d43-89d862e6b7ec",
  "action": "forward",
  "severity": "Info",
  "resourceSetId": "ffffffff-ffff-ffff-ffff-ffffffffffff"
}
```

#### `severity`

The `severity` field reflects how Aembit handled the event, such as whether it could attest the Client Workload, retrieve the directive, and inject credentials. It’s independent of [`outcome.result`](#result), which reflects what happened to the traffic itself. For example, a `Passthrough` event can still have `Error` severity, and a `Modified` event can have `Info` severity.

| Value     | Meaning                                                                                                         |
| --------- | --------------------------------------------------------------------------------------------------------------- |
| `Info`    | Aembit processed the event normally.                                                                            |
| `Warning` | Aembit couldn’t complete part of its processing, such as credential injection, but still forwarded the traffic. |
| `Error`   | An error occurred in Aembit’s processing or in the response returned to the Client Workload.                    |

### `workload`

The `client` and `server` objects are each `null` when Aembit can’t identify that workload.

| Field         | Presence | Description                            |
| ------------- | -------- | -------------------------------------- |
| `client.id`   | Optional | The identifier of the Client Workload. |
| `client.name` | Optional | The name of the Client Workload.       |
| `server.id`   | Optional | The identifier of the Server Workload. |
| `server.name` | Optional | The name of the Server Workload.       |

Example `workload` block

```json
"workload": {
  "client": {
    "id": "7c466803-9dd4-4388-9e45-420c57a0432c",
    "name": "MCP Client App"
  },
  "server": {
    "id": "49183921-55ab-4856-a8fc-a032af695e0d",
    "name": "MCP Remote Server"
  }
}
```

### `network`

| Field                | Presence | Description                                                |
| -------------------- | -------- | ---------------------------------------------------------- |
| `clientWorkloadIP`   | Always   | The IP address of the Client Workload.                     |
| `clientWorkloadPort` | Always   | The port on the Client Workload.                           |
| `transportProtocol`  | Always   | The transport protocol, always `TCP`.                      |
| `serverWorkloadHost` | Always   | The hostname of the Server Workload.                       |
| `serverWorkloadIP`   | Always   | The IP address of the Server Workload.                     |
| `serverWorkloadPort` | Always   | The port on the Server Workload.                           |
| `proxyPort`          | Always   | The local port on Agent Proxy that handled the connection. |

Example `network` block

```json
"network": {
  "clientWorkloadIP": "10.0.1.15",
  "clientWorkloadPort": 53134,
  "transportProtocol": "TCP",
  "serverWorkloadHost": "mcp.example.com",
  "serverWorkloadIP": "10.0.2.30",
  "serverWorkloadPort": 443,
  "proxyPort": 8080
}
```

### `outcome`

| Field    | Presence | Description                                                                                                                    |
| -------- | -------- | ------------------------------------------------------------------------------------------------------------------------------ |
| `result` | Always   | How Agent Proxy handled the traffic. See [`result`](#result).                                                                  |
| `reason` | Optional | Additional context about the outcome. Added when present, most often on `Error` results and sometimes on `Passthrough` events. |

#### `result`

| Value         | Meaning                                                                         |
| ------------- | ------------------------------------------------------------------------------- |
| `Passthrough` | The request or response passed through Agent Proxy unchanged.                   |
| `Modified`    | Agent Proxy modified the request or response, such as by injecting credentials. |
| `Error`       | Agent Proxy generated an error response to the Client Workload.                 |

From Agent Proxy 1.34.5755 on, a response that Aembit generates in place of the Server Workload’s response, such as a [Content Security](../../access-policies/content-security/overview.md) block, produces one response event. That event carries the generated response’s [`meta.severity`](#severity), `outcome.result`, and `outcome.reason`. Earlier versions record no response event for a response that Aembit generates.

#### `reason`

The `reason` field explains the outcome. It typically accompanies an `Error` result, but can also appear on a `Passthrough` event when Aembit couldn’t complete part of its processing, such as retrieving the directive.

When multiple error sources exist for a single event, `reason` reports the highest-priority one, in the order: Agent Proxy errors, then directive errors, then Server Workload errors.

Example `outcome` block

```json
"outcome": {
  "result": "Error",
  "reason": "Unexpected content type from upstream"
}
```
