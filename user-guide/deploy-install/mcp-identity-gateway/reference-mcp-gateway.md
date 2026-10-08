---
type: reference
title: "MCP Identity Gateway reference"
description: "Reference for the MCP Identity Gateway—token formats, proxied methods, connectivity, workload events, and self-hosted operations."
resource: https://docs.aembit.io/user-guide/deploy-install/mcp-identity-gateway/reference-mcp-gateway/
interface: mcp
tags: ["mcp-identity-gateway", "deploy-install"]
timestamp: 2026-10-07T18:20:48-07:00
---

# MCP Identity Gateway reference

Operational reference for the MCP Identity Gateway, covering the Aembit-managed service and self-hosted deployments.

For Tenant-side configuration, see [Set up the MCP Identity Gateway](../../access-policies/mcp-identity-gateway/setup-mcp-gateway.md). To deploy and operate the MCP Identity Gateway yourself, see [Self-host the MCP Identity Gateway](self-host-mcp-gateway.md).

## Token and credential details

The tokens and credentials used in each hop have different formats and purposes:

| Token / Credential | Format                                     | Source                                                                                                                |
| ------------------ | ------------------------------------------ | --------------------------------------------------------------------------------------------------------------------- |
| Agent-to-Gateway   | Aembit-issued access token (typically JWT) | Issued by the Aembit Authorization Server after the user authenticates via a configured IdP                           |
| Gateway-to-Server  | Varies by MCP server                       | Determined by the Credential Provider configuration (for example, OAuth 2.0 access token via Authorization Code flow) |

* **Agent-to-Gateway tokens** - The Aembit Authorization Server issues these tokens after it authenticates the user via an external identity provider (such as Google, Okta, or Microsoft Entra ID). The MCP Identity Gateway validates these tokens using Aembit’s signing keys, whether the keys sign with RS256 or ES256. A token’s audience must be the MCP Identity Gateway’s `/mcp` endpoint URL, or the MCP Identity Gateway URL itself with or without a trailing slash. The MCP Identity Gateway accepts a token for up to 60 seconds after it expires.
* **Gateway-to-Server credentials** - Aembit manages these via Credential Providers. For modern SaaS MCP servers, these are typically OAuth 2.0 access tokens obtained via the Authorization Code (3-legged OAuth) flow. Aembit may support other methods depending on how the MCP server authenticates.
* **Credential caching** - The MCP Identity Gateway caches downstream MCP server credentials and configuration in memory to reduce latency. Cached credentials are short-lived and refreshed as needed; the MCP Identity Gateway doesn’t persist them to disk.

## Proxied MCP methods

The MCP Identity Gateway proxies the following MCP protocol methods to downstream MCP servers. All methods go through the same token validation, policy evaluation, and credential injection flow.

The MCP Identity Gateway serves clients on either the [2026-07-28 revision](https://modelcontextprotocol.io/specification/2026-07-28) of the MCP specification or an earlier revision. The proxied methods apply to both, with revision-specific compatibility behavior where the two revisions differ. The MCP Identity Gateway negotiates each downstream connection, so a client’s revision and a server’s revision don’t have to match.

### Capability advertisement

When an MCP client connects, the MCP Identity Gateway advertises the union of the capabilities that its assigned MCP servers support. These capabilities include tools, resources, prompts, and tasks. This set comes from the assigned MCP servers, not from a fixed list and not from what the client declared.

Advertising a capability is separate from letting a client use it. Tools, resources, and prompts are server capabilities: the MCP Identity Gateway advertises them on behalf of its assigned MCP servers, and a client doesn’t declare them. Task handling and elicitation come from the client. A client on the 2026-07-28 revision declares them on each request, and a client on an earlier revision declares them when it connects:

* A task request from a client that didn’t declare tasks fails with error `-32021`.
* The MCP Identity Gateway can relay a request for input to a client on a revision earlier than 2026-07-28. If that client didn’t declare elicitation, the request fails with error `-32600`. See [Elicitation](#elicitation).

### Tool methods

| Method       | Description                                                                                                                                                                                                                                       |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `tools/list` | Discovers available tools across all assigned MCP servers and returns each under a prefixed name. See [Tool and prompt names](#tool-and-prompt-names). The response includes tool annotations from upstream servers when those servers send them. |
| `tools/call` | Invokes a tool on the appropriate MCP server.                                                                                                                                                                                                     |

### Tool and prompt names

The MCP Identity Gateway names each tool and prompt `<server-workload>_<name>`, so names from different MCP servers stay apart. `<server-workload>` is the name of the Server Workload the tool or prompt came from, with every character other than `A`-`Z`, `a`-`z`, `0`-`9`, or a hyphen replaced by a hyphen. For example, the `create_issue` tool from a Server Workload named `github.prod` reaches the MCP client as `github-prod_create_issue`.

MCP Tool Access Control matches the name the MCP server publishes, not the prefixed name the MCP client receives. See [MCP tool name reference](../../access-policies/content-security/mcp-tool-access-control/reference.md).

> **Two Server Workloads can’t share a prefix**
>
> Server Workload names that differ only in characters the MCP Identity Gateway replaces produce the same prefix. `github.prod` and `github prod` both become `github-prod`. Two Server Workloads assigned to the same MCP client can share a prefix. The MCP Identity Gateway then leaves both workloads’ tools and prompts out of `tools/list` and `prompts/list`, and calls to them fail with error `-32603`. The MCP Identity Gateway logs the two Server Workloads and the prefix they share. Rename one of the Server Workloads to fix it.

### Resource methods

| Method           | Description                                                                                                                                             |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `resources/list` | Discovers available resources across all assigned MCP servers. The MCP Identity Gateway fans out the request to all servers and aggregates the results. |
| `resources/read` | Retrieves a specific resource by URI from the appropriate MCP server.                                                                                   |

> **A shared resource URI reads from only one MCP server**
>
> The MCP Identity Gateway returns each resource URI exactly as the MCP server reported it. It prefixes each resource’s `name` the same way as a [tool name](#tool-and-prompt-names), and puts the name of the Server Workload that returned it in brackets ahead of the resource’s `title` and `description`, so an MCP client can tell resources apart in `resources/list`. The MCP client sends the URI unchanged in `resources/read`. Two MCP servers can expose the same URI. The MCP Identity Gateway then sends every read for that URI to the first of them in the MCP client’s assignments that listed or read the URI. If a later read from that MCP server fails, the MCP Identity Gateway doesn’t try the other one. The MCP client can’t choose which of the two resources it reads.

### Prompt methods

| Method         | Description                                                                                                                                              |
| -------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `prompts/list` | Discovers available prompts across all assigned MCP servers and returns each under a prefixed name. See [Tool and prompt names](#tool-and-prompt-names). |
| `prompts/get`  | Retrieves a specific prompt from the MCP server that exposes it.                                                                                         |

### Task methods

Tasks let an MCP server accept work that outlives a single call and report on it later.

| Method         | Description                                                              |
| -------------- | ------------------------------------------------------------------------ |
| `tasks/get`    | Returns the current state of a task from the MCP server that created it. |
| `tasks/update` | Sends input a task is waiting on to the MCP server that created it.      |
| `tasks/cancel` | Cancels a task on the MCP server that created it.                        |

### Elicitation

An upstream MCP server can ask for more input before it finishes a call. How the MCP Identity Gateway handles the request depends on the revision the MCP server and the client use:

* When both use the 2026-07-28 revision, the client receives the server’s request for input, answers it, and retries the call.
* When the MCP server uses the 2026-07-28 revision and the client uses an earlier one, the MCP Identity Gateway relays the request as `elicitation/create`. It sends the client’s answer to the MCP server. The client must declare elicitation. The MCP Identity Gateway doesn’t relay sampling or roots requests to these clients.
* When the MCP server uses an earlier revision and sends `elicitation/create`, the MCP Identity Gateway declines it, and the client never receives it.

### Methods the MCP Identity Gateway answers itself

* `resources/templates/list` and `completion/complete` return an empty result.
* `ping` returns a result for clients on revisions earlier than 2026-07-28, and error `-32601` for clients on the 2026-07-28 revision.
* `logging/setLevel`, `resources/subscribe`, `resources/unsubscribe`, `subscriptions/listen`, `tasks/list`, and `tasks/result` return error `-32601`. The MCP Identity Gateway doesn’t send change notifications to MCP clients.

### Transport

The MCP Identity Gateway uses the streamable HTTP transport. A client on a revision earlier than 2026-07-28 can send an HTTP `GET` request with its session identifier to the `/mcp` endpoint. The MCP Identity Gateway answers it with a stream of messages. For a client on the 2026-07-28 revision, an HTTP `GET` request returns `405 Method Not Allowed`.

## Session management

Sessions belong to the 2025-11-25 revision of the MCP specification and earlier revisions. A client on the 2026-07-28 revision sends no session identifier, and the MCP Identity Gateway holds no session for it. When such a client reaches an MCP server running an earlier revision, the MCP Identity Gateway performs that server’s handshake and manages the upstream session itself.

MCP clients can end their session with the MCP Identity Gateway by sending an HTTP `DELETE` request to the `/mcp` endpoint with the `mcp-session-id` header set to the session identifier. The MCP Identity Gateway returns `202 Accepted` on success. Subsequent requests that reuse the deleted session ID return `404 Not Found`.

```shell
curl -X DELETE "https://<gateway-host>/mcp" \
  -H "Authorization: Bearer <token>" \
  -H "mcp-session-id: <session-id>"
# Expected: 202 Accepted
```

This behavior implements [session management](https://modelcontextprotocol.io/specification/2025-11-25/basic/transports#session-management) in the MCP specification.

The MCP Identity Gateway also ends a session that stays idle, and a self-hosted MCP Identity Gateway keeps sessions in memory unless you configure a session store. For both, see [Session persistence](session-persistence-mcp-gateway.md).

## Connectivity requirements

Aembit operates the MCP Identity Gateway endpoint at `https://<tenantId>.mcpgateway.aembit.io` (replace `<tenantId>` with your Aembit Tenant ID). For MCP clients to reach the MCP Identity Gateway, the network paths from your AI agent hosts must allow outbound HTTPS on port `443` to this hostname.

| Source                  | Destination                               | Port | Purpose                                           |
| ----------------------- | ----------------------------------------- | ---- | ------------------------------------------------- |
| MCP clients / AI agents | `https://<tenantId>.mcpgateway.aembit.io` | 443  | MCP requests over TLS                             |
| MCP clients / AI agents | Your IdP (Okta, Google, Entra ID, etc.)   | 443  | User authentication during the initial OAuth flow |

MCP clients authenticate using access tokens (JWTs) issued by the Aembit Authorization Server after the user authenticates through your configured IdP. The MCP Identity Gateway validates tokens against the configured Trust Provider and uses streamable HTTP transport for server-to-client streaming.

Aembit manages the MCP Identity Gateway’s outbound paths to the Aembit Cloud control plane, MCP servers, and IdP discovery endpoints, so these don’t require customer configuration.

## Logging and events

### Log access

Because Aembit operates the MCP Identity Gateway, the Aembit operations team manages runtime logs—customers don’t access them directly. For customer-facing visibility into MCP activity, use **workload events** in Aembit Cloud (see the next section) and forward them via [Log Streams](../../administration/log-streams/overview.md) to your SIEM or observability tooling.

If you self-host the MCP Identity Gateway, you access its runtime logs directly on the host. See [Logs](#logs) under [Self-hosted operations](#self-hosted-operations).

### Workload events

Workload events in Aembit Cloud capture access patterns for audit and observability. See [Audit and report on Workload activity](../../audit-report/overview.md) for details.

#### The `userId` field

When an [identity provider](../../access-policies/trust-providers/overview.md) authenticates the MCP client, `mcp.request` and `mcp.response` workload events include a `userId` field containing the subject of the user’s OAuth or OIDC access token. This lets you attribute MCP activity to the specific authenticated user in audit reports.

The `userId` field is absent when Aembit can’t identify the MCP client, such as when client workload identification fails.

> **Event coverage**
>
> Workload events capture **Gateway to MCP Server** traffic only. This includes:
>
> * Tool invocations forwarded to upstream MCP servers
> * Resource requests forwarded to upstream MCP servers
> * Credential injection events
> * Policy evaluation results for server access
>
> **Agent to Gateway** events (such as initial client connections and authentication) don’t appear in workload events. Aembit plans this capability for a future release.
>
> Until Agent-to-Gateway events are available in workload events, if you need connection or authentication visibility, contact your Aembit representative.

## Observability

The MCP Identity Gateway produces structured JSON logs that help you:

* Answer “who did what” questions—which user and AI agent accessed which MCP server and tools, and when
* Trace policy decisions—which policy allowed or denied a given request
* Monitor behavior—connection patterns and error rates between AI agents and MCP servers

Forward these logs to [Log Streams](../../administration/log-streams/overview.md) to integrate with your existing observability and Security Information and Event Management (SIEM) tooling.

## Operational considerations

* **Policy management** - Configure access policies through the [Aembit Tenant](../../access-policies/overview.md), [Terraform provider](../../access-policies/advanced-options/terraform/terraform-configuration.md), or [API](../../../dev-guide/api/overview.md).
* **Service management** - Aembit operates the MCP Identity Gateway as a managed service. The Aembit operations team handles provisioning, upgrades, TLS certificate management, and runtime health.
* **Customer-facing observability** - Use workload events in Aembit Cloud and forward via [Log Streams](../../administration/log-streams/overview.md) for visibility into MCP activity.
* **Specification revisions** - The MCP Identity Gateway supports the 2026-07-28 revision of the MCP specification and earlier revisions. It serves each client on the revision the client asks for, when the MCP Identity Gateway supports that revision.

To verify your Tenant configuration is working correctly, see [Verify the connection](../../access-policies/mcp-identity-gateway/setup-mcp-gateway.md#verify-the-connection) in the setup guide.

## Deployment model

Aembit operates the MCP Identity Gateway as a managed service. Each Aembit Tenant has a per-Tenant MCP Identity Gateway endpoint at `https://<tenantId>.mcpgateway.aembit.io`.

* Aembit provisions, operates, and upgrades the MCP Identity Gateway.
* Aembit handles TLS termination, certificate management, and runtime operations.
* Customers configure only Aembit Tenant resources (Identity Provider, Trust Provider, Access Policies, Credential Providers).

To request an MCP Identity Gateway endpoint for your Tenant, contact your Aembit representative.

As a secondary option, you can self-host the MCP Identity Gateway in your own infrastructure. See [Self-host the MCP Identity Gateway](self-host-mcp-gateway.md) and [Self-hosted operations](#self-hosted-operations).

## Self-hosted operations

The following sections apply only when you [self-host the MCP Identity Gateway](self-host-mcp-gateway.md). For the Aembit-managed service, Aembit handles service management, networking, logging, and metrics for you.

### Service management

A self-hosted MCP Identity Gateway runs as a `systemd` service named `aembit_mcp_gateway`.

```shell
# Check service status
sudo systemctl status aembit_mcp_gateway


# Restart the service
sudo systemctl restart aembit_mcp_gateway


# Stop the service
sudo systemctl stop aembit_mcp_gateway


# Start the service
sudo systemctl start aembit_mcp_gateway


# Follow logs
journalctl --namespace aembit_mcp_gateway -f
```

### Network requirements

Open the following network paths for the host running a self-hosted MCP Identity Gateway.

#### Inbound

| Port | Protocol | Purpose                                                     |
| ---- | -------- | ----------------------------------------------------------- |
| 443  | TCP/TLS  | MCP client connections                                      |
| 80   | TCP      | TLS certificate provisioning (Let’s Encrypt)                |
| 9091 | TCP/HTTP | Prometheus metrics (configurable via `AEMBIT_METRICS_PORT`) |

#### Outbound

| Port | Target           | Destination                    | Purpose                                |
| ---- | ---------------- | ------------------------------ | -------------------------------------- |
| 443  | Aembit Cloud     | `https://<tenantId>.aembit.io` | Authorization, policy, and credentials |
| 443  | MCP servers      | `https://<mcp-server-host>`    | Proxied MCP traffic                    |
| 443  | IdP endpoints    | `https://<idp-host>/...`       | OAuth/OIDC user authentication         |
| 5000 | Agent Controller | `http://localhost:5000`        | Registration (localhost only)          |

> **Agent Controller dependency**
>
> The Agent Controller is a colocated Aembit Edge component that registers the MCP Identity Gateway with Aembit Cloud and provides credentials and configuration. A self-hosted MCP Identity Gateway requires a running Agent Controller to start and operate. For details, see [About the Agent Controller](../about-agent-controller.md).

### Logs

A self-hosted MCP Identity Gateway writes logs to journald. View them using `journalctl`:

```shell
# Follow logs in real-time
journalctl --namespace aembit_mcp_gateway -f


# View recent logs
journalctl --namespace aembit_mcp_gateway -n 100


# View logs since a specific time
journalctl --namespace aembit_mcp_gateway --since "1 hour ago"
```

### Prometheus metrics

The MCP Identity Gateway exposes a Prometheus-compatible metrics endpoint for integration with observability tools.

#### Endpoint

The metrics endpoint is available at `/metrics` on a configurable port (default `9091`). To override the port, set `AEMBIT_METRICS_PORT` during installation. See [MCP Identity Gateway environment variables](env-vars-mcp-gateway.md) for details.

The default port is 9091 to avoid a collision with the Agent Controller, which exposes its metrics on port 9090 on the same host.

#### Process and runtime metrics

| Metric                       | Type      | Labels                  | Description                                                 |
| ---------------------------- | --------- | ----------------------- | ----------------------------------------------------------- |
| `machine_cpu_cores`          | `gauge`   | `component`, `hostname` | Number of CPU cores available to the MCP Identity Gateway   |
| `version`                    | `gauge`   | `component`, `version`  | MCP Identity Gateway version                                |
| `process_cpu_seconds_total`  | `counter` | `component`, `hostname` | CPU seconds consumed by the MCP Identity Gateway process    |
| `process_memory_usage_bytes` | `gauge`   | `component`, `hostname` | Memory consumed by the MCP Identity Gateway process (bytes) |

#### Session metrics

| Metric                                                          | Type      | Labels      | Description                                                                                                                                  |
| --------------------------------------------------------------- | --------- | ----------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| `aembit_mcp_gateway_sessions_created_total`                     | `counter` | `tenant_id` | MCP sessions the Gateway created since it started                                                                                            |
| `aembit_mcp_gateway_sessions_active`                            | `gauge`   | `tenant_id` | MCP sessions that are open. The Gateway recomputes this value from the session store on an interval, so it can lag the true count            |
| `aembit_mcp_gateway_session_cleanup_last_run_timestamp_seconds` | `gauge`   | none        | Unix timestamp of the last completed session cleanup run, for the whole process. A value that stops advancing means the cleanup task stopped |

#### Request processing metrics

| Metric                                            | Type        | Labels                           | Description                                                                                   |
| ------------------------------------------------- | ----------- | -------------------------------- | --------------------------------------------------------------------------------------------- |
| `aembit_mcp_gateway_mcp_requests_processed_total` | `counter`   | `tenant_id`, `method`, `outcome` | MCP requests the Gateway processed                                                            |
| `aembit_mcp_gateway_mcp_request_duration_seconds` | `histogram` | `tenant_id`, `method`            | Time the Gateway spent processing an MCP request, excluding time spent waiting on MCP servers |

Fanout requests always report success here

For a request the Gateway fans out to your MCP servers, it builds the client response itself, whatever the upstream servers return. Those requests count as `outcome="success"` in `aembit_mcp_gateway_mcp_requests_processed_total`. To see upstream health, use `aembit_mcp_gateway_upstream_mcp_server_fanout_workloads_failed_total` against `aembit_mcp_gateway_upstream_mcp_server_fanout_workloads_total`.

#### Upstream MCP server metrics

| Metric                                                                      | Type        | Labels                           | Description                                                                                                                                                                                                                          |
| --------------------------------------------------------------------------- | ----------- | -------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `aembit_mcp_gateway_upstream_mcp_server_http_requests_total`                | `counter`   | `tenant_id`, `method`, `outcome` | HTTP requests the Gateway sent to upstream MCP servers                                                                                                                                                                               |
| `aembit_mcp_gateway_upstream_mcp_server_mcp_responses_total`                | `counter`   | `tenant_id`, `method`, `outcome` | MCP protocol responses received from upstream servers. Counts HTTP 2xx responses only                                                                                                                                                |
| `aembit_mcp_gateway_upstream_mcp_server_response_bytes`                     | `histogram` | `tenant_id`, `method`            | Size in bytes of each HTTP response body received from an upstream server. Recorded whatever the status code, so an oversized error page is as visible as an oversized success body                                                  |
| `aembit_mcp_gateway_upstream_mcp_server_fanout_timeouts_total`              | `counter`   | `tenant_id`, `method`, `outcome` | Upstream workloads that timed out during a fanout request                                                                                                                                                                            |
| `aembit_mcp_gateway_upstream_mcp_server_fanout_duration_seconds`            | `histogram` | `tenant_id`, `method`            | Time a fanout request took across all its upstream workloads                                                                                                                                                                         |
| `aembit_mcp_gateway_upstream_mcp_server_fanout_workloads_total`             | `counter`   | `tenant_id`, `method`            | Upstream workloads that fanout requests attempted                                                                                                                                                                                    |
| `aembit_mcp_gateway_upstream_mcp_server_fanout_workloads_failed_total`      | `counter`   | `tenant_id`, `method`            | Upstream workloads that failed within a fanout request, through a transport error, a timeout, a non-2xx status, or an MCP-level error. A failed count equal to the attempted count means the upstream fleet was down for that method |
| `aembit_mcp_gateway_upstream_mcp_server_sessions_restored_count_total`      | `counter`   | `tenant_id`, `outcome`           | Upstream sessions restored from the Gateway session cache                                                                                                                                                                            |
| `aembit_mcp_gateway_upstream_mcp_server_sessions_reinitialized_count_total` | `counter`   | `tenant_id`, `outcome`           | Upstream sessions re-established after the upstream session expired                                                                                                                                                                  |

#### Authentication and Content Security metrics

| Metric                                           | Type      | Labels                 | Description                                                                                                                                                                                              |
| ------------------------------------------------ | --------- | ---------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `aembit_mcp_gateway_auth_failures_total`         | `counter` | `tenant_id`, `reason`  | Inbound requests the Gateway rejected at the authentication layer. Expired and invalid tokens are normal background traffic; a sustained rate, or a spike in `audience_mismatch`, is worth investigating |
| `aembit_mcp_gateway_jwks_refresh_attempts_total` | `counter` | `tenant_id`, `outcome` | JWKS load and refresh attempts                                                                                                                                                                           |
| `aembit_mcp_gateway_aidr_guard_decisions_total`  | `counter` | `tenant_id`, `outcome` | CrowdStrike AIDR scan decisions                                                                                                                                                                          |

#### Session store and health metrics

| Metric                                                        | Type        | Labels                           | Description                                                                                                                                                                                                                                 |
| ------------------------------------------------------------- | ----------- | -------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `aembit_mcp_gateway_session_store_operations_total`           | `counter`   | `operation`, `outcome`, `reason` | Session store operations                                                                                                                                                                                                                    |
| `aembit_mcp_gateway_session_store_operation_duration_seconds` | `histogram` | `operation`, `outcome`, `reason` | Time a session store operation took                                                                                                                                                                                                         |
| `aembit_mcp_gateway_readiness_probe_flaps_total`              | `counter`   | `probe`                          | Times a readiness probe went from healthy to unhealthy, for the whole process. The Gateway counts a flap after the probe crosses its consecutive-failure threshold, which is the point where the instance leaves the load balancer rotation |

#### Label values

| Label       | Applies to                                                     | Values                                                                   |
| ----------- | -------------------------------------------------------------- | ------------------------------------------------------------------------ |
| `component` | Process and runtime metrics                                    | `aembit_mcp_gateway`                                                     |
| `hostname`  | Process and runtime metrics                                    | The hostname of the machine running the Gateway                          |
| `version`   | The `version` metric                                           | The MCP Identity Gateway build version, such as `1.34.5733`              |
| `tenant_id` | Every metric with a `tenant_id` label                          | Your Aembit Tenant ID                                                    |
| `method`    | Every metric with a `method` label                             | The MCP method name, such as `initialize`, `tools/list`, or `tools/call` |
| `outcome`   | Every metric with an `outcome` label, except where noted       | `success` or `error`                                                     |
| `outcome`   | `aembit_mcp_gateway_aidr_guard_decisions_total`                | `scanned`, `fail_open`, or `fail_closed`                                 |
| `outcome`   | `aembit_mcp_gateway_upstream_mcp_server_fanout_timeouts_total` | `timeout`                                                                |
| `reason`    | `aembit_mcp_gateway_auth_failures_total`                       | `missing_header`, `audience_mismatch`, or `invalid_token`                |
| `reason`    | Session store metrics                                          | `none`, `not_found`, or `store_error`                                    |
| `operation` | Session store metrics                                          | `save`, `get`, `delete`, or `count`                                      |
| `probe`     | `aembit_mcp_gateway_readiness_probe_flaps_total`               | `cloud` or `session_store`                                               |

Histogram metrics expose `_bucket`, `_count`, and `_sum` series under the metric names in these tables.

#### Scraping configuration

Configure Prometheus to scrape the metrics endpoint:

```yaml
scrape_configs:
  - job_name: 'aembit-mcp-gateway'
    static_configs:
      - targets: ['<gateway-host>:9091']
```

Replace `<gateway-host>` with your MCP Identity Gateway hostname or IP address.

## Supported MCP servers

The MCP Identity Gateway supports both third-party SaaS MCP providers and customer-built MCP servers, subject to compatibility and configuration.

Aembit has validated the MCP Identity Gateway with a small set of MCP servers. Additional MCP servers may work but Aembit considers them best-effort until explicitly documented.

The MCP Identity Gateway connects to an MCP server on either the 2026-07-28 revision or an earlier one. It tries the newer revision first and falls back to the earlier handshake when the server doesn’t accept it, so a server on either revision works with no configuration.

## Security guarantees and non-goals

**Guarantees:**

* The Identity Gateway authenticates every request before any processing—unauthenticated requests receive `401` immediately and are never forwarded to MCP servers
* AI agents never receive downstream credentials for MCP servers
* Centrally managed Aembit Access Policies govern all access
* Aembit enforces TLS end-to-end: the MCP Identity Gateway terminates TLS at its endpoint and initiates new TLS connections to MCP servers

**Non-goals:**

* The MCP Identity Gateway doesn’t replace the MCP server’s internal authorization logic
* The MCP Identity Gateway doesn’t inspect or filter prompt content beyond what policy evaluation requires

## See also

* [MCP Identity Gateway concepts](concepts-mcp-gateway.md) - Architecture, identity model, and access policies
* [Environment variables](env-vars-mcp-gateway.md) - Operator reference (the environment variables Aembit sets when provisioning an MCP Identity Gateway)
* [Set up the MCP Identity Gateway](../../access-policies/mcp-identity-gateway/setup-mcp-gateway.md) - Tenant configuration, from Trust Provider to Access Policy
* [Access Policies](../../access-policies/overview.md) - How Aembit authorizes each MCP request
