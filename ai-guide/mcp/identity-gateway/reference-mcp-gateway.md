---
type: reference
title: "MCP Identity Gateway reference"
description: "Reference for the MCP Identity Gateway—token formats, proxied methods, connectivity, workload events, and self-hosted operations."
resource: https://docs.aembit.io/ai-guide/mcp/identity-gateway/reference-mcp-gateway/
interface: mcp
tags: ["identity-gateway", "mcp"]
timestamp: 2026-08-27T18:01:41-07:00
---

# MCP Identity Gateway reference

Operational reference for the MCP Identity Gateway, covering the Aembit-managed service and self-hosted deployments.

For Tenant-side configuration, see [Set up the MCP Identity Gateway](setup-mcp-gateway.md). To deploy and operate the Gateway yourself, see [Self-host the MCP Identity Gateway](self-host-mcp-gateway.md).

## Token and credential details

The tokens and credentials used in each hop have different formats and purposes:

| Token / Credential | Format                                     | Source                                                                                                                |
| ------------------ | ------------------------------------------ | --------------------------------------------------------------------------------------------------------------------- |
| Agent-to-Gateway   | Aembit-issued access token (typically JWT) | Issued by the Aembit Authorization Server after the user authenticates via a configured IdP                           |
| Gateway-to-Server  | Varies by MCP server                       | Determined by the Credential Provider configuration (for example, OAuth 2.0 access token via Authorization Code flow) |

* **Agent-to-Gateway tokens** - The Aembit Authorization Server issues these tokens after it authenticates the user via an external identity provider (such as Google, Okta, or Microsoft Entra ID). The MCP Gateway validates these tokens using Aembit’s signing keys.
* **Gateway-to-Server credentials** - Aembit manages these via Credential Providers. For modern SaaS MCP servers, these are typically OAuth 2.0 access tokens obtained via the Authorization Code (3-legged OAuth) flow. Aembit may support other methods depending on how the MCP server authenticates.
* **Credential caching** - The MCP Gateway caches downstream MCP server credentials and configuration in memory to reduce latency. Cached credentials are short-lived and refreshed as needed; the Gateway doesn’t persist them to disk.

## Proxied MCP methods

The MCP Identity Gateway proxies the following MCP protocol methods to downstream MCP servers. All methods go through the same token validation, policy evaluation, and credential injection flow.

### Tool methods

| Method       | Description                                                                                                                                                                                                                     |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `tools/list` | Discovers available tools across all assigned MCP servers. The Gateway adds prefixes to prevent tool name collisions across servers. The response includes tool annotations from upstream servers when those servers send them. |
| `tools/call` | Invokes a tool on the appropriate MCP server.                                                                                                                                                                                   |

### Resource methods

| Method           | Description                                                                                                                                |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| `resources/list` | Discovers available resources across all assigned MCP servers. The Gateway fans out the request to all servers and aggregates the results. |
| `resources/read` | Retrieves a specific resource by URI from the appropriate MCP server.                                                                      |

> **No resource prefixing**
>
> When the Gateway fans out `resources/list` across multiple MCP servers, it returns resource URIs as-is without adding server-specific prefixes. If two MCP servers expose resources with the same URI, both appear in the aggregated list. This differs from tool discovery, where the Gateway adds prefixes to tool names to prevent collisions.

### Unsupported methods

The MCP Identity Gateway uses streamable HTTP transport, not Server-Sent Events (SSE). HTTP `GET` requests to the `/mcp` endpoint return `405 Method Not Allowed`, per the MCP specification.

## Session management

MCP clients can end their session with the Gateway by sending an HTTP `DELETE` request to the `/mcp` endpoint with the `mcp-session-id` header set to the session identifier. The Gateway returns `204 No Content` on success. Subsequent requests that reuse the deleted session ID return `404 Not Found`.

```shell
curl -X DELETE "https://<gateway-host>/mcp" \
  -H "Authorization: Bearer <token>" \
  -H "mcp-session-id: <session-id>"
# Expected: 204 No Content
```

This behavior implements [MCP specification section 2.5.5](https://modelcontextprotocol.io/specification/2025-03-26/basic/transports#session-management).

The Gateway also ends a session that stays idle, and a self-hosted Gateway keeps sessions in memory unless you configure a session store. For both, see [Session persistence](session-persistence-mcp-gateway.md).

## Connectivity requirements

Aembit operates the Gateway endpoint at `https://<tenantId>.mcpgateway.aembit.io` (replace `<tenantId>` with your Aembit Tenant ID). For MCP clients to reach the Gateway, the network paths from your AI agent hosts must allow outbound HTTPS on port `443` to this hostname.

| Source                  | Destination                               | Port | Purpose                                           |
| ----------------------- | ----------------------------------------- | ---- | ------------------------------------------------- |
| MCP clients / AI agents | `https://<tenantId>.mcpgateway.aembit.io` | 443  | MCP requests over TLS                             |
| MCP clients / AI agents | Your IdP (Okta, Google, Entra ID, etc.)   | 443  | User authentication during the initial OAuth flow |

MCP clients authenticate using access tokens (JWTs) issued by the Aembit Authorization Server after the user authenticates through your configured IdP. The Gateway validates tokens against the configured Trust Provider and uses streamable HTTP transport for server-to-client streaming.

Aembit manages the Gateway’s outbound paths to the Aembit Cloud control plane, MCP servers, and IdP discovery endpoints, so these don’t require customer configuration.

## Logging and events

### Log access

Because Aembit operates the Gateway, the Aembit operations team manages runtime logs—customers don’t access them directly. For customer-facing visibility into MCP activity, use **workload events** in Aembit Cloud (see the next section) and forward them via [Log Streams](../../../user-guide/administration/log-streams/overview.md) to your SIEM or observability tooling.

If you self-host the Gateway, you access its runtime logs directly on the host. See [Logs](#logs) under [Self-hosted operations](#self-hosted-operations).

### Workload events

Workload events in Aembit Cloud capture access patterns for audit and observability. See [Audit and report on Workload activity](../../../user-guide/audit-report/overview.md) for details.

#### The `userId` field

When an [identity provider](../../../user-guide/access-policies/trust-providers/overview.md) authenticates the MCP client, `mcp.request` and `mcp.response` workload events include a `userId` field containing the subject of the user’s OAuth or OIDC access token. This lets you attribute MCP activity to the specific authenticated user in audit reports.

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

Forward these logs to [Log Streams](../../../user-guide/administration/log-streams/overview.md) to integrate with your existing observability and Security Information and Event Management (SIEM) tooling.

## Operational considerations

* **Policy management** - Configure access policies through the [Aembit Tenant](../../../user-guide/access-policies/overview.md), [Terraform provider](../../../user-guide/access-policies/advanced-options/terraform/terraform-configuration.md), or [API](../../../dev-guide/api/overview.md).
* **Service management** - Aembit operates the Gateway as a managed service. The Aembit operations team handles provisioning, upgrades, TLS certificate management, and runtime health.
* **Customer-facing observability** - Use workload events in Aembit Cloud and forward via [Log Streams](../../../user-guide/administration/log-streams/overview.md) for visibility into MCP activity.

To verify your Tenant configuration is working correctly, see [Verify the connection](setup-mcp-gateway.md#verify-the-connection) in the setup guide.

## Deployment model

Aembit operates the MCP Identity Gateway as a managed service. Each Aembit Tenant has a per-Tenant Gateway endpoint at `https://<tenantId>.mcpgateway.aembit.io`.

* Aembit provisions, operates, and upgrades the Gateway.
* Aembit handles TLS termination, certificate management, and runtime operations.
* Customers configure only Aembit Tenant resources (Identity Provider, Trust Provider, Access Policies, Credential Providers).

To request a Gateway endpoint for your Tenant, contact your Aembit representative.

As a secondary option, you can self-host the Gateway in your own infrastructure. See [Self-host the MCP Identity Gateway](self-host-mcp-gateway.md) and [Self-hosted operations](#self-hosted-operations).

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

Open the following network paths for the host running a self-hosted Gateway.

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
> The Agent Controller is a colocated Aembit Edge component that registers the MCP Identity Gateway with Aembit Cloud and provides credentials and configuration. A self-hosted MCP Identity Gateway requires a running Agent Controller to start and operate. For details, see [About the Agent Controller](../../../user-guide/deploy-install/about-agent-controller.md).

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

| Metric                                            | Type        | Labels                           | Description                                     |
| ------------------------------------------------- | ----------- | -------------------------------- | ----------------------------------------------- |
| `aembit_mcp_gateway_mcp_requests_processed_total` | `counter`   | `tenant_id`, `method`, `outcome` | MCP requests the Gateway processed              |
| `aembit_mcp_gateway_mcp_request_duration_seconds` | `histogram` | `tenant_id`, `method`            | Time the Gateway took to process an MCP request |

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

The Gateway supports both third-party SaaS MCP providers and customer-built MCP servers, subject to compatibility and configuration.

Aembit has validated the Gateway with a small set of MCP servers. Additional MCP servers may work but Aembit considers them best-effort until explicitly documented.

## Security guarantees and non-goals

**Guarantees:**

* The Identity Gateway authenticates every request before any processing—unauthenticated requests receive `401` immediately and are never forwarded to MCP servers
* AI agents never receive downstream credentials for MCP servers
* Centrally managed Aembit policies govern all access
* Aembit enforces TLS end-to-end: the Gateway terminates TLS at its endpoint and initiates new TLS connections to MCP servers

**Non-goals:**

* The Gateway doesn’t replace the MCP server’s internal authorization logic
* The Gateway doesn’t inspect or filter prompt content beyond what policy evaluation requires

## See also

* [MCP Identity Gateway concepts](concepts-mcp-gateway.md) - Architecture, identity model, and access policies
* [Environment variables](env-vars-mcp-gateway.md) - Operator reference (the environment variables Aembit sets when provisioning a Gateway)
