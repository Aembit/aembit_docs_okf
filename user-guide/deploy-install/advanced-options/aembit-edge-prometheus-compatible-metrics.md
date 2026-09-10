---
type: reference
title: "Aembit Edge Prometheus-compatible metrics"
description: "How to view Aembit Edge Prometheus-compatible metrics"
resource: https://docs.aembit.io/user-guide/deploy-install/advanced-options/aembit-edge-prometheus-compatible-metrics/
interface: web-ui
tags: ["advanced-option", "deploy-install"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Aembit Edge Prometheus-compatible metrics

Aembit Edge Components (Agent Controller, Agent Proxy, Agent Injector) expose Prometheus-compatible metrics so you can monitor each component and troubleshoot issues from your existing observability tooling.

## Prometheus configuration

Aembit exposes Prometheus-compatible metrics in Kubernetes and Virtual Machine deployments. The following sections describe configuration steps for each deployment model. You can use any observability tool that scrapes Prometheus-compatible metrics.

### Configuring Prometheus (Kubernetes)

The following steps show an example of how to configure a “vanilla” Prometheus instance in a Kubernetes cluster. Depending on your Kubernetes cluster configuration, you may need to perform a different set of steps to configure Prometheus for your cluster.

1. Open a terminal window in your environment and run the following command.

   `kubectl edit configmap prometheus-server`

2. Edit the `prometheus.yaml` configuration file by adding the following code snippet before the `kubernetes-pods` section:

   ```shell
       - honor_labels: true
         job_name: kubernetes-pods-aembit
         kubernetes_sd_configs:
         - role: pod
         relabel_configs:
         - action: keep
           regex: true
           source_labels:
             - __meta_kubernetes_pod_annotation_aembit_io_metrics_scrape
         - action: replace
           regex: (.+)
           source_labels:
             - __meta_kubernetes_pod_annotation_aembit_io_metrics_path
           target_label: __metrics_path__
         - action: replace
           regex: (\d+);(([A-Fa-f0-9]{1,4}::?){1,7}[A-Fa-f0-9]{1,4})
           replacement: "[$2]:$1"
           source_labels:
             - __meta_kubernetes_pod_annotation_aembit_io_metrics_port
             - __meta_kubernetes_pod_ip
           target_label: __address__
         - action: replace
           regex: (\d+);((([0-9]+?)(\.|$)){4})
           replacement: $2:$1
           source_labels:
             - __meta_kubernetes_pod_annotation_aembit_io_metrics_port
             - __meta_kubernetes_pod_ip
           target_label: __address__
         - action: labelmap
           regex: __meta_kubernetes_pod_label_(.+)
         - action: replace
           source_labels:
             - __meta_kubernetes_namespace
           target_label: namespace
         - action: replace
           source_labels:
             - __meta_kubernetes_pod_name
           target_label: pod
         - action: drop
           regex: Pending|Succeeded|Failed|Completed
           source_labels:
             - __meta_kubernetes_pod_phase
         - action: replace
           source_labels:
             - __meta_kubernetes_pod_node_name
           target_label: node
   ```

   The example code block allows Prometheus to automatically detect Aembit annotations and scrape Agent Proxy metrics.

3. Save your changes in the `prometheus.yaml` configuration file.

#### Kubernetes annotations

Agent Controller and Agent Proxy come with standard Prometheus annotations, enabling Prometheus to automatically discover and scrape metrics from these Aembit Edge Components.

Since Agent Proxy runs as part of the Client Workload, which may already expose Prometheus metrics and have its own annotations, Aembit introduces a dedicated set of annotations. Add these annotations to Client Workload pods without conflicting with existing annotations.

Aembit automatically adds the following annotations to the Client Workload where Agent Proxy runs:

* `aembit.io/metrics-scrape` - Default value is `true`.
* `aembit.io/metrics-path` - Default value is `/metrics`.
* `aembit.io/metrics-port` - Default value is `9099`. This is a default metrics port used by Agent Proxy to expose metrics.

You may override these annotations, `aembit.io/metrics-port` to adjust metrics port on Agent Proxy.

#### Helm variables

The following Helm variables turn metrics on or off:

* agentController.metrics.enabled
* agentInjector.metrics.enabled
* agentProxy.metrics.enabled

### Configuring Prometheus (Virtual Machine)

Configure metrics scraping by editing `/etc/prometheus/prometheus.yml`. Replace `example.vm.local:port` with Agent Controller and Agent Proxy VM hostname and the port on which each metrics server listens. For Agent Controller, set the port to **9090**. For Agent Proxy, set the port to **9099**.

```yaml
scrape_configs:
  - job_name: 'vm-monitoring'
    static_configs:
      - targets: ['example.vm.local:<port>']
```

#### Virtual Machine environment variables

Pass these environment variables to Agent Controller installer to manage metrics functionality.

* **AEMBIT\_METRICS\_ENABLED** - enabled for both Agent Controller and Agent Proxy
* **AEMBIT\_METRICS\_PORT** - available only for Agent Proxy, not Agent Controller. The self-hosted MCP Identity Gateway accepts this variable on its own installer. See [MCP Identity Gateway environment variables](../../../ai-guide/mcp/identity-gateway/env-vars-mcp-gateway.md#aembit_metrics_port).

## Aembit Edge Prometheus metrics

Aembit Edge Components expose Prometheus-compatible metrics that you can view in any observability tool that scrapes Prometheus-compatible metrics.

The following sections list the Prometheus-compatible metrics that Aembit Edge Components expose, along with the labels you can use to filter results and drill down into specific data.

### Agent Proxy metrics

Agent Proxy exposes the following Prometheus-compatible metrics.

* `aembit_agent_proxy_incoming_connections_total` - The total number of incoming connections (connections established from a Client Workload to Agent Proxy).

  * labels:

    * `application_protocol`: `http`, `snowflake`, `postgres`, `redshift`, `mysql`, `redis`, `oracleDatabase`, `unspecified`
    * `resource_set_id` (optional): `<resource_set_id>`
    * `client_workload_id` (optional): `<client_workload_id>`
    * `server_workload_id` (optional): `<server_workload_id>`

* `aembit_agent_proxy_active_incoming_connections` - The number of active incoming connection (connections established from a Client Workload to Agent Proxy).

  * labels:

    * `application_protocol`: `http`, `snowflake`, `postgres`, `redshift`, `mysql`, `redis`, `oracleDatabase`, `unspecified`
    * `resource_set_id` (optional): `<resource_set_id>`
    * `client_workload_id` (optional): `<client_workload_id>`
    * `server_workload_id` (optional): `<server_workload_id>`

* `aembit_agent_proxy_credential_injections_total` - The total number of credentials injected by Agent Proxy.

  * labels:

    * `application_protocol`: `http`, `snowflake`, `postgres`, `redshift`, `mysql`, `redis`, `oracleDatabase`, `unspecified`
    * success: `success`, `failure`.

* `aembit_agent_proxy_token_expiration_unix_timestamp` - The expiration timestamp for Aembit Agent Proxy Token (to access Aembit Cloud).

* `aembit_agent_proxy_aembit_cloud_connection_status` - The current connection status between Agent Proxy and Aembit Cloud. If the connection is up, the result is “1” (Connected). If the status is down, the result is “0” (Disconnected).

* `aembit_agent_proxy_credentials_cached_entries_total` - The total number of unexpired credentials cached by Agent Proxy.

  * labels:
    * `resource_set_id` (optional): `<resource_set_id>`

* `aembit_agent_proxy_directives_cached_entries_total` - The total number of unexpired directives cached by Agent Proxy.

  * labels:
    * `resource_set_id` (optional): `<resource_set_id>`

* `version` - Agent Proxy version.

  * labels:

    * component: `aembit_agent_proxy`
    * version: `version: <version>`

* `process_cpu_second_total` - The amount of CPU seconds used by Agent Proxy. This value could be more than the wall clock time if Agent Proxy used more than one core. This metric is useful in conjunction with `machine_cpu_cores` to calculate CPU % usage.

  * labels:

    * component: `aembit_agent_proxy`
    * hostname: `hostname: <hostname>`

* `machine_cpu_cores` - The number of CPU cores available to Agent Proxy.

  * labels:

    * component: `aembit_agent_proxy`
    * hostname: `hostname: <hostname>`

* `process_memory_usage_bytes` - The amount of memory (in bytes) used by Agent Proxy.

  * labels:

    * component: `aembit_agent_proxy`
    * hostname: `hostname: <hostname>`

### Agent Controller metrics

Agent Controller exposes the following Prometheus-compatible metrics.

* `aembit_agent_controller_token_expiration_unix_timestamp` - The expiration timestamp for Aembit Agent Controller Token (to access Aembit Cloud).

* `aembit_agent_controller_access_token_requests_total` - The number of Agent Controller requests to get access token (for Agent Controller use).

  * label

    * Result: `success`, `failure`
    * `Agent_Controller_Id`: `<AgentControllerID>`

* `aembit_agent_controller_proxy_token_requests_total` - The number of Agent Proxy requests received by the Agent Controller to get access token.

  * labels

    * Result: success, `failure`
    * `Agent_Controller_Id` (optional): `<AgentControllerID>`

* `aembit_agent_controller_registration_status` - Agent Controller registration status. Status can be either: `0` (Not Registered) or `1` (Registered).

  * labels
    * `Agent_Controller_Id` (optional): `<AgentControllerID>`

* `version` - Agent Controller version.

  * labels

    * component: `aembit_agent_controller`
    * version: `<version>`

### Agent Injector metrics

Agent Injector exposes the following Prometheus-compatible metrics.

* `aembit_injector_pods_seen_total` - The number of pods processed by Agent Injector.

* `aembit_injector_pods_injection_total` - The number of pods that received injected Aembit Edge Components.

  * label
    * `success`: “success” or “failure”

### MCP Identity Gateway metrics

The MCP Identity Gateway exposes a Prometheus-compatible metrics endpoint for integration with observability tools.

#### Endpoint

The metrics endpoint is available at `/metrics` on a configurable port (default `9091`). To override the port, set `AEMBIT_METRICS_PORT` during installation. See [MCP Identity Gateway environment variables](../../../ai-guide/mcp/identity-gateway/env-vars-mcp-gateway.md) for details.

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
