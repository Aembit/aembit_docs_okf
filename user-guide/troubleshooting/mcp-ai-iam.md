---
type: troubleshooting
title: "Troubleshoot MCP and AI IAM access"
description: "Investigate MCP and AI IAM failures end-to-end across Aembit's authorization and gateway reporting surfaces."
resource: https://docs.aembit.io/user-guide/troubleshooting/mcp-ai-iam/
interface: web-ui
tags: ["troubleshooting"]
timestamp: 2026-10-28T15:13:06-07:00
---

# Troubleshoot MCP and AI IAM access

When an AI agent or MCP client can’t reach an MCP server, the failure can originate in the MCP Authorization Server or in the MCP Identity Gateway.

Use this guide to:

* Investigate failures across the three Aembit reporting surfaces
* Look up specific failure modes and resolutions
* Monitor proactively for new failures

> **Client setup troubleshooting**
>
> If your error happens during MCP client setup (redirect URI, OAuth callback issues), see [Troubleshoot the MCP Authorization Server](../deploy-install/mcp-authorization-server/troubleshooting-mcp-auth-server.md). Come back here when those errors point to a runtime authorization or MCP Identity Gateway issue.

## Step-by-step investigation

Three reporting surfaces in the Aembit Tenant cover MCP and AI IAM activity. Consult them in this order:

### Access Authorization Events

Use Access Authorization Events to confirm whether the request reached Aembit Cloud and how Access Policy evaluation resolved.

1. In the Aembit Tenant, click **Reporting**. The Access Authorization Events tab opens by default.
2. Set **Timespan** to a range that covers the failure.
3. Set **Severity** to **Error**.
4. View the event type. It tells you which stage of authorization failed and what to check first. See the [Error condition matrix](#error-condition-matrix) for specific resolutions.

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

| Failure                                            | Where it surfaces                                                                                                                                                               | What you’ll see                                                                                                                                                                                                                                                                                                                                               | Resolution                                                                                                                                                                                                                                                                                                                                                                                     |
| -------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Client registration failure                        | `access.registration` event, severity Error                                                                                                                                     | Dynamic Client Registration (DCR) rejected the request, and the MCP client receives `invalid_redirect_uri` or `invalid_client_metadata`. The `clientWorkload.reason` names the check that failed, such as `NoMatchingClientWorkload` for the redirect URI or `MissingGrantTypes`. A `server_error` response means Aembit Cloud hit an internal error instead. | Fix the registration request or the Client Workload that `clientWorkload.reason` points to; retrying the same request fails the same way. See [Client registration failures](../deploy-install/mcp-authorization-server/troubleshooting-mcp-auth-server.md#client-registration-failures). For `server_error`, contact [Aembit support](https://aembit.io/contact) if the error repeats. |
| Client Workload redirect URI mismatch              | `access.authorization` event, severity Info. A client that registers through DCR hits this earlier, as an `access.registration` event with `reason` `NoMatchingClientWorkload`. | `outcome.result` is `Unauthorized` and `clientWorkload.reason` is `NoClientWorkload`: the redirect URI in the request doesn’t match any registered Client Workload.                                                                                                                                                                                           | Verify the redirect URI on the Client Workload matches the MCP client’s callback URL exactly. See [Redirect URI mismatch](../deploy-install/mcp-authorization-server/troubleshooting-mcp-auth-server.md#redirect-uri-mismatch).                                                                                                                                                         |
| Server Workload mismatch                           | `access.authorization` event, severity Info                                                                                                                                     | `outcome.result` is `Unauthorized` and `serverWorkload.reason` is `NoServerWorkload`: the resource URL in the request doesn’t match any registered Server Workload.                                                                                                                                                                                           | Verify the resource URL on the Server Workload matches the MCP server URL the client is trying to reach.                                                                                                                                                                                                                                                                                       |
| Trust Provider attestation failure                 | `access.authorization` event, severity Error                                                                                                                                    | Trust Provider `result` is `Unauthorized` with a `reason` such as `MatchRuleFailed` or `InvalidSignature`.                                                                                                                                                                                                                                                    | Confirm the identity token’s claims match the Trust Provider match rules. See [Authorization failure](../audit-report/access-authorization-events.md#authorization-failure).                                                                                                                                                                                                            |
| Credential Provider failure                        | `access.credential` event, severity Error                                                                                                                                       | Credential Provider `result` isn’t `Retrieved`. Common reasons include `Token expired` and upstream identity-provider errors.                                                                                                                                                                                                                                 | The expired-credential explanation in the event now describes which token expired and at which step. Re-authenticate the user or refresh the credential.                                                                                                                                                                                                                                       |
| Enterprise-managed token exchange failure          | `access.credential` event, severity Error                                                                                                                                       | Credential Provider `result` is `Error` and `reason` is generic. The event doesn’t say which of the two token requests failed.                                                                                                                                                                                                                                | Work through [Enterprise-managed token exchange failures](#enterprise-managed-token-exchange-failures).                                                                                                                                                                                                                                                                                        |
| No matching Client Workload                        | `access.discovery` event, severity Error                                                                                                                                        | Discovery enumerates the Client Workloads considered for the Gateway-to-Server Access Policy and indicates that none matched. When a Client Workload matched some of its identifiers but not all, the entry’s `reason`, `attribute`, `expectedValue`, and `actualValue` name the identifier that failed.                                                      | Verify the Client Workload identifying the Gateway is present in the Gateway-to-Server Access Policy.                                                                                                                                                                                                                                                                                          |
| No matching Server Workload                        | `access.discovery` event, severity Error                                                                                                                                        | Discovery enumerates the Server Workloads considered and indicates that none matched the target MCP server. The entry’s `reason` says no Server Workload matched.                                                                                                                                                                                             | Verify the Server Workload for the target MCP server exists and the Gateway-to-Server Access Policy includes it.                                                                                                                                                                                                                                                                               |
| No active Access Policy                            | `access.discovery` event, severity Error                                                                                                                                        | Discovery identifies the Client Workload, but the `accessPolicies` entry’s `reason` says no Access Policy matched.                                                                                                                                                                                                                                            | Verify an active Gateway-to-Server Access Policy includes the Client Workload identifying the Gateway.                                                                                                                                                                                                                                                                                         |
| Access Policy match fails during MCP authorization | `access.authorization` event, severity Info                                                                                                                                     | The MCP client shows `Aembit Access Policy validation failed`, followed by the event ID when Aembit recorded one. The `reason` on the failed entry names the match that failed.                                                                                                                                                                               | Look up the event ID in Access Authorization Events and fix the match the `reason` names. Exactly one active Access Policy must connect the Client Workload and Server Workload.                                                                                                                                                                                                               |
| Trust Provider attestation failure                 | `access.authorization` event, severity Error                                                                                                                                    | Same shape as the Client-to-Gateway Access Policy Trust Provider failure, but with the Gateway-to-Server Access Policy context.                                                                                                                                                                                                                               | Confirm the Aembit-issued JWT presented by the Gateway is valid for the target MCP server.                                                                                                                                                                                                                                                                                                     |
| Wrong-audience JWT presented to the Gateway        | `mcp.response` Workload Event, severity Error                                                                                                                                   | Gateway rejects the request because the access token’s `aud` claim doesn’t match the Gateway URL.                                                                                                                                                                                                                                                             | Reissue the access token with the correct audience. Check the Client Workload’s resource configuration.                                                                                                                                                                                                                                                                                        |
| No assigned MCP servers                            | `mcp.response` Workload Event, severity Error                                                                                                                                   | The Gateway received a request but has no Server Workloads assigned in the matched Gateway-to-Server Access Policy.                                                                                                                                                                                                                                           | Add the target Server Workload to the Gateway-to-Server Access Policy.                                                                                                                                                                                                                                                                                                                         |
| MCP server unreachable                             | `mcp.response` Workload Event, severity Error                                                                                                                                   | The Gateway couldn’t connect to the upstream MCP server. The reason describes the network or TLS failure.                                                                                                                                                                                                                                                     | Verify the Gateway can reach the MCP server over the network and confirm any required TLS configuration.                                                                                                                                                                                                                                                                                       |
| Non-2xx response from the MCP server               | `mcp.response` Workload Event, severity Error                                                                                                                                   | The MCP server returned a non-2xx HTTP status code. The reason includes the status code and any upstream error detail.                                                                                                                                                                                                                                        | Investigate the MCP server logs for the cause of the upstream error.                                                                                                                                                                                                                                                                                                                           |
| `tools/call` failure                               | `mcp.response` Workload Event, severity Error                                                                                                                                   | The Gateway forwarded the tool invocation, and the MCP server returned a tool-call error.                                                                                                                                                                                                                                                                     | Inspect the upstream MCP server response. The Gateway records the failure with Error severity so SIEM alerting can trigger on it.                                                                                                                                                                                                                                                              |

## Enterprise-managed token exchange failures

The [MCP Enterprise Managed Access Token](../access-policies/credential-providers/about-mcp-enterprise-managed-access-token.md) Credential Provider follows Enterprise-Managed Authorization and obtains a token in two requests. The first is an identity assertion request to the corporate identity provider. The second is an access token request to the MCP server’s authorization server. When either request fails, the `access.credential` event records result `Error` with a generic reason, so the following checklist is the diagnostic path.

* **The `offline_access` scope is missing.** The OIDC Identity Provider’s **Identity Provider Scopes** field must include `offline_access`, or Aembit receives no refresh token for the user.
* **The user hasn’t signed in since you added the scope.** Aembit captures the refresh token at sign-in. The user signs out of Aembit and signs in again through the Identity Provider.
* **The identity provider refused the identity assertion request.** Check the application’s grant types, its Cross-app access settings, and the AI agent’s Resource Connection in the identity provider. [Prepare the Identity Provider for enterprise-managed access](../access-policies/credential-providers/mcp-enterprise-managed-access-token-idp.md) describes each.
* **The MCP server’s authorization server refused the access token request.** Confirm the Credential Provider’s **OIDC Issuer URL** matches the issuer the identity provider trusts for the server. Also confirm that the **Client ID** is the one the server issued for Aembit.
* **The MCP server’s endpoints changed after Discover ran.** Endpoints are static after **Discover**. Open the Credential Provider, click **Discover** again, and save.

After you fix the cause, the user sends a new request through the MCP Identity Gateway and the next `access.credential` event shows result `Retrieved`.

## Match events across surfaces

To match Access Authorization Events with Workload Events for the same activity, use these shared IDs:

* **Context ID** to match Access Authorization Events from the same evaluation.
* **Connection ID** to match Workload Events on the same connection.
* **MCP Session ID** to match Workload Events across multiple connections in the same MCP session.
* **Authorization chain** to follow a request across both surfaces, back to the authorization decisions behind it.

| Identifier              | Where it appears                                                                                                                | What it identifies                                                                                                                                                                                                                                                                                                                  |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Event ID**            | All events (`meta.eventId`)                                                                                                     | A single event record. Unique per event. Useful when sharing a specific event with Aembit support.                                                                                                                                                                                                                                  |
| **Context ID**          | Access Authorization Events (`meta.contextId`)                                                                                  | An authorization evaluation context. The `access.request` and `access.authorization` events for the same evaluation share a Context ID.                                                                                                                                                                                             |
| **Authorization chain** | Workload Events, and `access.request` and `access.authorization` events from Aembit Edge policy requests (`authorizationChain`) | An ordered list of the Context IDs for the authorization decisions behind a request. The only identifier that spans both surfaces. To pivot from one surface to the other, filter Access Authorization Events by any Context ID in the chain. Workload Events carry it from MCP Identity Gateway 1.32 and Agent Proxy 1.34.5755 on. |
| **Connection ID**       | Workload Events (`meta.connectionId`)                                                                                           | A single TCP or HTTP connection through Agent Proxy or the MCP Identity Gateway. All request and response events for the same connection share a Connection ID.                                                                                                                                                                     |
| **MCP Session ID**      | Workload Events (`application.mcp.mcpSessionId`)                                                                                | The MCP protocol session between an MCP client and the Gateway. Shared across requests and responses in the same session. Useful when an MCP client reconnects mid-session.                                                                                                                                                         |
| **User identity**       | MCP Workload Events (`application.mcp.userId`)                                                                                  | The user identity associated with the event for MCP flows that involve a human identity, such as MCP Authorization Server flows. Use this with the **User (MCP App Protocol only)** filter to scope investigation to a single user.                                                                                                 |

### Trace a request with the authorization chain

When you investigate an MCP request, you need to understand which authorization decisions allowed the request, and what happened at each step. The authorization chain connects those events across both the access and workload surfaces.

An `authorizationChain` is an ordered list of the Context IDs for the authorization decisions behind the request. These events carry it:

* The `access.request` and `access.authorization` events from Aembit Edge policy requests.
* Workload Events from MCP Identity Gateway 1.32 and later, and from Agent Proxy 1.34.5755 and later.

MCP Authorization Server `/authorize` events and the events from credential retrieval don’t carry a chain. Their Context IDs still appear in the chains of the events that follow them. Each authorization decision adds its own Context ID to the chain, so the chain grows as the request moves through the flow. A workload event carries the chain as it stood when Aembit recorded the event.

The Client-to-Gateway workload event’s chain holds the Context IDs of the authorization events from access token issuance. The Gateway-to-Server workload event extends that chain with the directive and credential decisions.

To trace a request end-to-end:

1. Open the event you’re investigating and copy any Context ID from its `authorizationChain`.
2. In Access Authorization Events, filter by **Context ID** using that value.
3. Review the matching `access.request`, `access.authorization`, and `access.credential` events to see the Access Policy evaluation, the Trust Provider and Access Condition results, and the credential retrieval behind the request.

#### Requests that fan out to multiple servers

A request such as `tools/list` fans out to every assigned MCP server, and the Gateway runs a separate authorization for each upstream server. Each branch carries its own chain. The chains share the opening Context IDs from the Client-to-Gateway request (auth-code issuance and token exchange), then extend differently for each server.

| Event                                         | Chain (`authorizationChain`)                       |
| --------------------------------------------- | -------------------------------------------------- |
| `mcp.request` (Client-to-Gateway)             | `[11111111-…, 22222222-…]`                         |
| `mcp.request` to Server A (Gateway-to-Server) | `[11111111-…, 22222222-…, aaaa0001-…, aaaa0002-…]` |
| `mcp.request` to Server B (Gateway-to-Server) | `[11111111-…, 22222222-…, bbbb0001-…, bbbb0002-…]` |
| `mcp.request` for Server A (fanout)           | `[11111111-…, 22222222-…]`                         |
| `mcp.request` for Server B (fanout)           | `[11111111-…, 22222222-…]`                         |
| `mcp.response` (Client-to-Gateway)            | `[11111111-…, 22222222-…]`                         |

The Client-to-Gateway `mcp.response` carries only the shared opening Context IDs, not the per-branch entries, so you can trace each branch on its own. To investigate one server, take a Context ID from that branch’s chain and filter Access Authorization Events on it. For example, filtering on `aaaa0001-…` returns the `access.*` events for the connection to Server A.

The MCP Identity Gateway emits the Client-to-Gateway `mcp.request` and `mcp.response` events for every request that passes access token validation.

On `tools/list`, `resources/list`, and `prompts/list`, the Gateway also emits a fanout `mcp.request` event for each server, and an `mcp.response` event for each server that fails or times out. These are separate from the Gateway-to-Server events in the table. They name the server’s Server Workload in `workload.server.id` and `workload.server.name`. Their `authorizationChain` holds only the shared opening Context IDs, so use `workload.server.id` to tell the servers apart. To find a server’s Access Authorization Events, take a Context ID from that server’s Gateway-to-Server event. When known, the `mcp.response` event also records the MCP server’s host in `network.serverWorkloadHost` and the HTTP status the MCP server returned in `application.mcp.httpResponseCode`. When the server didn’t respond within the list timeout, `application.mcp.httpResponseCode` is `504`.

#### When an event has no chain

If an event has no `authorizationChain`, one of these applies:

* The event type doesn’t carry a chain, such as MCP Authorization Server `/authorize` events and the events from credential retrieval.
* The event came from an MCP Identity Gateway version earlier than 1.32 or an Agent Proxy version earlier than 1.34.5755.
* The chain exceeded its limit of 100 Context IDs, and Aembit Edge dropped it to keep request headers small.

Aembit still records the events; correlate them using the other identifiers in the preceding table.

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

On the single-server path, the MCP Identity Gateway emits two events per request. Fanout requests such as `tools/list` emit more events, and the count grows with the number of MCP servers the request fans out to. On `tools/list`, `resources/list`, and `prompts/list`, these include an `mcp.request` event for each of those MCP servers, and an `mcp.response` event for each one that fails or times out. A server the Gateway skips because it doesn’t support the method still gets its `mcp.request` event. Plan retention and alerting thresholds accordingly.

## Related pages

* [Access Authorization Events](../audit-report/access-authorization-events.md)
* [Workload Events](../audit-report/workload-events/overview.md)
* [MCP Authorization Tracing](../audit-report/mcp-authorization-tracing.md)
* [Troubleshoot the MCP Authorization Server](../deploy-install/mcp-authorization-server/troubleshooting-mcp-auth-server.md)
* [About the MCP Enterprise Managed Access Token Credential Provider](../access-policies/credential-providers/about-mcp-enterprise-managed-access-token.md)
* [Prepare the Identity Provider for enterprise-managed access](../access-policies/credential-providers/mcp-enterprise-managed-access-token-idp.md)
* [Log Streams](../administration/log-streams/overview.md)
