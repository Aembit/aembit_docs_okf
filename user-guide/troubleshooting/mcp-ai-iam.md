---
type: troubleshooting
title: "Troubleshoot MCP and AI IAM access"
description: "Investigate MCP and AI IAM failures end-to-end across Aembit's authorization and gateway reporting surfaces."
resource: https://docs.aembit.io/user-guide/troubleshooting/mcp-ai-iam/
interface: web-ui
tags: ["troubleshooting"]
timestamp: 2026-06-30T13:21:27-04:00
---

# Troubleshoot MCP and AI IAM access

When an AI agent or MCP client can’t reach an MCP server, the failure can originate in the

MCP Authorization Server or in MCP Identity Gateway.

Use this guide to:

* Investigate failures across the three Aembit reporting surfaces
* Look up specific failure modes and resolutions
* Monitor proactively for new failures

> **Client setup troubleshooting**
>
> If your error happens during MCP client setup (redirect URI, OAuth callback issues), see [Troubleshoot the MCP Authorization Server](../../ai-guide/mcp/authorization-server/troubleshooting-mcp-auth-server.md). Come back here when those errors point to a runtime authorization or MCP Identity Gateway issue.

## Step-by-step investigation

Three reporting surfaces in the Aembit Tenant cover MCP and AI IAM activity. Consult them in this order:

### Access Authorization Events

Use Access Authorization Events to confirm whether the request reached Aembit Cloud and how Access Policy evaluation resolved.

1. In the Aembit Tenant, click **Reporting**. The Access Authorization Events tab is selected by default.

2. Set **Timespan** to a range that covers the failure.

3. Set **Severity** to **Error**.

4. View the event type. It tells you which stage of authorization failed and what to check first. See the [Error condition matrix](#error-condition-matrix) for specific resolutions.

   * [`access.request`](../audit-report/access-authorization-events.md#access-request-events) - The request reached Aembit Cloud and a Client Workload match was attempted. An error means no Client Workload matched. Check that the Client Workload’s redirect URI matches the MCP client’s callback URL, and that the resource URL matches a registered Server Workload.
   * [`access.authorization`](../audit-report/access-authorization-events.md#access-authorization-events) - A Client Workload and Access Policy matched, and Aembit Cloud evaluated Trust Providers, Access Conditions, and the Server Workload selection. An error means one of those checks failed. Inspect the `result` field on each Trust Provider, Access Condition, and Server Workload entry in the event to see which rejected the request.
   * [`access.discovery`](../audit-report/access-authorization-events.md#access-discovery-events) - Aembit Cloud searched for matching workloads or policies and either found none or found multiple.
   * [`access.credential`](../audit-report/access-authorization-events.md#access-credential-events) - Aembit attempted to retrieve a credential from a Credential Provider for the matched Server Workload. An error means the Credential Provider didn’t return a usable credential. Check the `reason` field—`Token expired` means the user needs to reauthenticate; other reasons describe upstream identity-provider errors.

### Workload Events

Use Workload Events when Access Authorization Events show a successful authorization but the MCP client still received an error. See what the MCP Identity Gateway forwarded, modified, or rejected.

1. Open [Workload Events](../audit-report/workload-events/overview.md) and filter by the same timespan and `Application Protocol = MCP`.
2. If you know which user reported the failure, apply the **User (MCP App Protocol only)** filter. MCP Workload Events record user identity as `application.mcp.userId` for flows that involve a human identity.
3. Narrow by matched Client Workload, Server Workload, or `mcpSessionId` to isolate a specific session.
4. Look for [`mcp.response`](../audit-report/workload-events/overview.md#workload-response-events) events where `outcome.result` is `Error`. The `outcome.reason` field describes the failure source.

> **Event delay**
>
> Access Authorization Events and Workload Events can take a few minutes to appear in Reporting. If a recent failure doesn’t appear, refresh after a few minutes or use MCP Authorization Tracing for rapid visibility.

### MCP Authorization Tracing

Use MCP Authorization Tracing when the static event record is ambiguous and you can reproduce the failure on demand.

1. Ask the user to retry the failing operation.
2. Watch [MCP Authorization Tracing](../audit-report/mcp-authorization-tracing.md) for the resource URL, redirect URI, and Access Policy match outcome.

## Error condition matrix

The following matrix maps MCP failure modes to the event that surfaces them and how to remediate.

| Failure                                     | Where it surfaces                             | What you’ll see                                                                                                                 | Resolution                                                                                                                                                                                                            |
| ------------------------------------------- | --------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Client Workload redirect URI mismatch       | `access.request` event, severity Error        | No matching Client Workload, or the redirect URI in the request does not match any registered Client Workload.                  | Verify the redirect URI on the Client Workload matches the MCP client’s callback URL exactly. See [Redirect URI mismatch](../../ai-guide/mcp/authorization-server/troubleshooting-mcp-auth-server.md#redirect-uri-mismatch). |
| Server Workload mismatch                    | `access.request` event, severity Error        | Resource URL in the request does not match any registered Server Workload.                                                      | Verify the resource URL on the Server Workload matches the MCP server URL the client is trying to reach.                                                                                                              |
| Trust Provider attestation failure          | `access.authorization` event, severity Error  | Trust Provider `result` is `Unauthorized` with a `reason` such as `MatchRuleFailed` or `InvalidSignature`.                      | Confirm the identity token’s claims match the Trust Provider match rules. See [Authorization failure](../audit-report/access-authorization-events.md#authorization-failure).                                   |
| Credential Provider failure                 | `access.credential` event, severity Error     | Credential Provider `result` is not `Retrieved`. Common reasons include `Token expired` and upstream identity-provider errors.  | The expired-credential explanation in the event now describes which token expired and at which step. Re-authenticate the user or refresh the credential.                                                              |
| No matching Client Workload                 | `access.discovery` event, severity Error      | Discovery enumerates the Client Workloads considered for the Gateway-to-Server Access Policy and indicates that none matched.   | Verify the Client Workload identifying the Gateway is present in the Gateway-to-Server Access Policy.                                                                                                                 |
| No matching Server Workload                 | `access.discovery` event, severity Error      | Discovery enumerates the Server Workloads considered and indicates that none matched the target MCP server.                     | Verify the Server Workload for the target MCP server exists and is included in the Gateway-to-Server Access Policy.                                                                                                   |
| Trust Provider attestation failure          | `access.authorization` event, severity Error  | Same shape as the Client-to-Gateway Access Policy Trust Provider failure, but with the Gateway-to-Server Access Policy context. | Confirm the Aembit-issued JWT presented by the Gateway is valid for the target MCP server.                                                                                                                            |
| Wrong-audience JWT presented to the Gateway | `mcp.response` Workload Event, severity Error | Gateway rejects the request because the access token’s `aud` claim does not match the Gateway URL.                              | Reissue the access token with the correct audience. Check the Client Workload’s resource configuration.                                                                                                               |
| No assigned MCP servers                     | `mcp.response` Workload Event, severity Error | The Gateway received a request but has no Server Workloads assigned in the matched Gateway-to-Server Access Policy.             | Add the target Server Workload to the Gateway-to-Server Access Policy.                                                                                                                                                |
| MCP server unreachable                      | `mcp.response` Workload Event, severity Error | The Gateway could not connect to the upstream MCP server. The reason describes the network or TLS failure.                      | Verify network reachability from the Gateway to the MCP server and confirm any required TLS configuration.                                                                                                            |
| Non-2xx response from the MCP server        | `mcp.response` Workload Event, severity Error | The MCP server returned a non-2xx HTTP status code. The reason includes the status code and any upstream error detail.          | Investigate the MCP server logs for the cause of the upstream error.                                                                                                                                                  |
| `tools/call` failure                        | `mcp.response` Workload Event, severity Error | The Gateway forwarded the tool invocation, and the MCP server returned a tool-call error.                                       | Inspect the upstream MCP server response. The Gateway records the failure with Error severity so SIEM alerting can trigger on it.                                                                                     |

## Match events across surfaces

To match Access Authorization Events with Workload Events for the same activity, use these shared IDs:

* **Context ID** to match Access Authorization Events from the same evaluation.
* **Connection ID** to match Workload Events on the same connection.
* **MCP Session ID** to match Workload Events across multiple connections in the same MCP session.
* **Authorization chain** to follow a request across both surfaces, back to the authorization decisions behind it.

| Identifier              | Where it appears                                                           | What it identifies                                                                                                                                                                                                                                                                                                                                                                   |
| ----------------------- | -------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Event ID**            | All events (`meta.eventId`)                                                | A single event record. Unique per event. Useful when sharing a specific event with Aembit support.                                                                                                                                                                                                                                                                                   |
| **Context ID**          | Access Authorization Events (`meta.contextId`)                             | An authorization evaluation context. The `access.request` and `access.authorization` events for the same evaluation share a Context ID.                                                                                                                                                                                                                                              |
| **Authorization chain** | Access Authorization Events and MCP Workload Events (`authorizationChain`) | An ordered list of the Context IDs for the authorization decisions behind a request. The only identifier that spans both surfaces: it appears on the MCP Workload Events and on the Access Authorization Events those decisions produced. To pivot from one surface to the other, filter Access Authorization Events by any Context ID in the chain. Requires version 1.32 or later. |
| **Connection ID**       | Workload Events (`meta.connectionId`)                                      | A single TCP or HTTP connection through Agent Proxy or the MCP Identity Gateway. All request and response events for the same connection share a Connection ID.                                                                                                                                                                                                                      |
| **MCP Session ID**      | Workload Events (`application.mcp.mcpSessionId`)                           | The MCP protocol session between an MCP client and the Gateway. Shared across requests and responses in the same session. Useful when an MCP client reconnects mid-session.                                                                                                                                                                                                          |
| **User identity**       | MCP Workload Events (`application.mcp.userId`)                             | The user identity associated with the event for MCP flows that involve a human identity, such as MCP Authorization Server flows. Use this with the **User (MCP App Protocol only)** filter to scope investigation to a single user.                                                                                                                                                  |

### Trace a request with the authorization chain

When you investigate an MCP request, you need to understand which authorization decisions allowed the request, and what happened at each step. The authorization chain connects those events across both the access and workload surfaces.

MCP events carry an `authorizationChain`, which is an ordered list of the Context IDs for the authorization decisions behind the request. Each authorization event adds its own Context ID to the chain, so the chain grows as the request moves through the flow. A workload event carries the chain as it stood when Aembit recorded the event.

The Client-to-Gateway workload event shares its chain with the authorization events from access token issuance. The Gateway-to-Server workload event extends that chain with the directive and credential decisions.

To trace a request end-to-end:

1. Open the event you’re investigating and copy any Context ID from its `authorizationChain`.
2. In Access Authorization Events, filter by **Context ID** using that value.
3. Review the matching `access.request`, `access.authorization`, and `access.credential` events to see the policy evaluation, the Trust Provider and Access Condition results, and the credential retrieval behind the request.

#### Requests that fan out to multiple servers

A request such as `tools/list` fans out to every assigned MCP server, and the Gateway runs a separate authorization for each upstream server. Each branch carries its own chain. The chains share the opening Context IDs from the Client-to-Gateway request (auth-code issuance and token exchange), then extend differently for each server.

| Event                                         | Chain (`authorizationChain`)                       |
| --------------------------------------------- | -------------------------------------------------- |
| `mcp.request` (Client-to-Gateway)             | `[11111111-…, 22222222-…]`                         |
| `mcp.request` to Server A (Gateway-to-Server) | `[11111111-…, 22222222-…, aaaa0001-…, aaaa0002-…]` |
| `mcp.request` to Server B (Gateway-to-Server) | `[11111111-…, 22222222-…, bbbb0001-…, bbbb0002-…]` |
| `mcp.response` (Client-to-Gateway)            | `[11111111-…, 22222222-…]`                         |

The Client-to-Gateway `mcp.response` carries only the shared opening Context IDs, not the per-branch entries, so you can trace each branch on its own. To investigate one server, take a Context ID from that branch’s chain and filter Access Authorization Events on it. For example, filtering on `aaaa0001-…` returns the `access.*` events for the connection to Server A.

#### When an event has no chain

If an event has no `authorizationChain`, either the request ran on a component earlier than version 1.32, or the chain exceeded its limit of 100 Context IDs. In the second case, Aembit Edge dropped the chain to keep request headers small. Aembit still records the events; correlate them using the other identifiers in the preceding table.

## Monitor for failures

Once you know what MCP and AI IAM failures look like, you can monitor for them proactively instead of waiting for a user to report a problem. Forward Aembit events to any destination you already operate—a SIEM, an S3 bucket, or another log store—using [Log Streams](../administration/log-streams/overview.md).

### What to alert on

* Any Access Authorization Event with severity `Error`. This includes Trust Provider failures, Credential Provider failures, and [`access.discovery`](../audit-report/access-authorization-events.md#access-discovery-events) events that found no match.
* Any Workload Event with severity `Error` and event type [`mcp.response`](../audit-report/workload-events/overview.md#workload-response-events). This catches Gateway-side rejections, upstream MCP server failures, and tool-call failures.

### Severity levels

* `Info` - Normal authorization or traffic event. No action required.
* `Warning` - Reserved for non-failure conditions that still merit review.
* `Error` - A real failure. Trust Provider attestation failures and tool-call failures now emit at `Error` severity in MCP flows, so you can use `Error` as a reliable alerting threshold.

### Event volume

The MCP Identity Gateway emits two events per request on the simple path and `2 + (2 × N)` events on fanout requests such as `tools/list` and `initialize`, where N is the number of assigned MCP servers. Plan retention and alerting thresholds accordingly.

## Related pages

* [Access Authorization Events](../audit-report/access-authorization-events.md)
* [Workload Events](../audit-report/workload-events/overview.md)
* [MCP Authorization Tracing](../audit-report/mcp-authorization-tracing.md)
* [Troubleshoot the MCP Authorization Server](../../ai-guide/mcp/authorization-server/troubleshooting-mcp-auth-server.md)
* [Log Streams](../administration/log-streams/overview.md)
