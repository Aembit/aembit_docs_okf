---
type: how-to
title: "Aembit Edge Prometheus-compatible metrics"
description: "How to view Aembit Edge Prometheus-compatible metrics"
resource: https://docs.aembit.io/user-guide/deploy-install/advanced-options/aembit-edge-prometheus-compatible-metrics/
interface: web-ui
tags: [advanced-option, deploy-install]
timestamp: 2026-04-30T15:52:15-04:00
type_inferred: true
---

# Aembit Edge Prometheus-compatible metrics

Aembit Edge Components (Agent Controller, Agent Proxy, Agent Injector) expose Prometheus-compatible metrics so you can monitor each component and troubleshoot issues from your existing observability tooling.

## Prometheus configuration

[Section titled “Prometheus configuration”](#prometheus-configuration)

Aembit exposes Prometheus-compatible metrics in Kubernetes and Virtual Machine deployments. The following sections describe configuration steps for each deployment model. You can use any observability tool that scrapes Prometheus-compatible metrics.

### Configuring Prometheus (Kubernetes)

[Section titled “Configuring Prometheus (Kubernetes)”](#configuring-prometheus-kubernetes)

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

[Section titled “Kubernetes annotations”](#kubernetes-annotations)

Agent Controller and Agent Proxy come with standard Prometheus annotations, enabling Prometheus to automatically discover and scrape metrics from these Aembit Edge Components.

Since Agent Proxy runs as part of the Client Workload, which may already expose Prometheus metrics and have its own annotations, Aembit introduces a dedicated set of annotations. Add these annotations to Client Workload pods without conflicting with existing annotations.

Aembit automatically adds the following annotations to the Client Workload where Agent Proxy runs:

* `aembit.io/metrics-scrape` - Default value is `true`.
* `aembit.io/metrics-path` - Default value is `/metrics`.
* `aembit.io/metrics-port` - Default value is `9099`. This is a default metrics port used by Agent Proxy to expose metrics.

You may override these annotations, `aembit.io/metrics-port` to adjust metrics port on Agent Proxy.

#### Helm variables

[Section titled “Helm variables”](#helm-variables)

The following Helm variables turn metrics on or off:

* agentController.metrics.enabled
* agentInjector.metrics.enabled
* agentProxy.metrics.enabled

### Configuring Prometheus (Virtual Machine)

[Section titled “Configuring Prometheus (Virtual Machine)”](#configuring-prometheus-virtual-machine)

Configure metrics scraping by editing `/etc/prometheus/prometheus.yml`. Replace `example.vm.local:port` with Agent Controller and Agent Proxy VM hostname and the port on which each metrics server listens. For Agent Controller, set the port to **9090**. For Agent Proxy, set the port to **9099**.

```yaml
scrape_configs:
  - job_name: 'vm-monitoring'
    static_configs:
      - targets: ['example.vm.local:<port>']
```

#### Virtual Machine environment variables

[Section titled “Virtual Machine environment variables”](#virtual-machine-environment-variables)

Pass these environment variables to Agent Controller installer to manage metrics functionality.

* **AEMBIT\_METRICS\_ENABLED** - enabled for both Agent Controller and Agent Proxy
* **AEMBIT\_METRICS\_PORT** - available only for Agent Proxy

## Aembit Edge Prometheus metrics

[Section titled “Aembit Edge Prometheus metrics”](#aembit-edge-prometheus-metrics)

Aembit Edge Components expose Prometheus-compatible metrics that you can view in any observability tool that scrapes Prometheus-compatible metrics.

The following sections list the Prometheus-compatible metrics that Aembit Edge Components expose, along with the labels you can use to filter results and drill down into specific data.

### Agent Proxy metrics

[Section titled “Agent Proxy metrics”](#agent-proxy-metrics)

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

[Section titled “Agent Controller metrics”](#agent-controller-metrics)

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

[Section titled “Agent Injector metrics”](#agent-injector-metrics)

Agent Injector exposes the following Prometheus-compatible metrics.

* `aembit_injector_pods_seen_total` - The number of pods processed by Agent Injector.

* `aembit_injector_pods_injection_total` - The number of pods that received injected Aembit Edge Components.

  * label
    * `success`: “success” or “failure”

### MCP Identity Gateway metrics

[Section titled “MCP Identity Gateway metrics”](#mcp-identity-gateway-metrics)

The MCP Identity Gateway exposes a Prometheus-compatible metrics endpoint for integration with observability tools.

#### Endpoint

The metrics endpoint is available at `/metrics` on a configurable port (default `9091`). To override the port, set `AEMBIT_METRICS_PORT` during installation. See [MCP Identity Gateway environment variables](../../../ai-guide/mcp/identity-gateway/env-vars-mcp-gateway.md) for details.

The default port is `9091` to avoid a collision with the Agent Controller, which exposes its metrics on port `9090` on the same host.

#### Available metrics

| Metric                       | Type    | Labels                  | Description                                                 |
| ---------------------------- | ------- | ----------------------- | ----------------------------------------------------------- |
| `machine_cpu_cores`          | gauge   | `component`, `hostname` | Number of CPU cores available to the MCP Identity Gateway   |
| `version`                    | gauge   | `component`, `version`  | MCP Identity Gateway version                                |
| `process_cpu_seconds_total`  | counter | `component`, `hostname` | CPU seconds consumed by the MCP Identity Gateway process    |
| `process_memory_usage_bytes` | gauge   | `component`, `hostname` | Memory consumed by the MCP Identity Gateway process (bytes) |

The `component` label value is `aembit_mcp_gateway`.

#### Scraping configuration

Configure Prometheus to scrape the metrics endpoint:

```yaml
scrape_configs:
  - job_name: 'aembit-mcp-gateway'
    static_configs:
      - targets: ['<gateway-host>:9091']
```

Replace `<gateway-host>` with your MCP Identity Gateway hostname or IP address.
