---
type: reference
title: "Edge Component environment variables reference"
description: "Reference for environment variables of Edge Components categorized by deployment type"
resource: https://docs.aembit.io/reference/edge-components/edge-component-env-vars/
tags: [edge-component]
timestamp: 2026-06-18T13:15:52-04:00
type_inferred: true
---

# Edge Component environment variables reference

The following sections list and describe the environment variables available for Edge Components:

* [Agent Controller](#agent-controller-environment-variables)

* [Agent Proxy](#agent-proxy-environment-variables)

* [Agent Injector](#agent-injector-environment-variables)

* [Aembit CLI](#aembit-cli-environment-variables)

## Agent Controller environment variables

[Section titled “Agent Controller environment variables”](#agent-controller-environment-variables)

Here is a list of all available environment variables for configuring the Agent Controller installer:

### `AEMBIT_AGENT_CONTROLLER_ID` Required

[Section titled “AEMBIT\_AGENT\_CONTROLLER\_ID ”](#aembit_agent_controller_id)

Default - not set

OS-All

Required if not using `AEMBIT_DEVICE_CODE`.

The Agent Controller ID, available in your tenant’s administrative console for each Agent Controller. This ID is utilized for Trust Provider**Trust Provider**: Trust Providers validate Client Workload identities through workload attestation, verifying identity claims from the workload's runtime environment rather than relying on pre-shared secrets.[Learn more](../../get-started/concepts/trust-providers.md) registration. You must provide either this or the `AEMBIT_DEVICE_CODE` environment variable.

*Example*:\
`01234567-89ab-cdef-0123-456789abcdef`

***

### `AEMBIT_DEVICE_CODE` Required

[Section titled “AEMBIT\_DEVICE\_CODE ”](#aembit_device_code)

Default - not set

OS-All

Required if not using `AEMBIT_AGENT_CONTROLLER_ID`.

The device code for the Agent Controller. Generate this code in your tenant’s administrative console for code-based registration. You must provide either this or the `AEMBIT_AGENT_CONTROLLER_ID` environment variable.

*Example*:\
`123456`

***

### `AEMBIT_TENANT_ID` Required

[Section titled “AEMBIT\_TENANT\_ID ”](#aembit_tenant_id)

Default - not set

OS-All

The Aembit Tenant**Aembit Tenant**: Aembit Tenants serve as isolated, dedicated environments within Aembit that provide complete separation of administrative domains and security configurations.[Learn more](../../get-started/concepts/administration.md) ID that the Agent Controller will register with.

*Example*:\
`123abc`

***

### `AEMBIT_HTTP_PORT_DISABLED`

[Section titled “AEMBIT\_HTTP\_PORT\_DISABLED”](#aembit_http_port_disabled)

Default - `false`

OS-All

When `true`, turns off HTTP support in Agent Controller, restricting communication to HTTPS only. When `false`, Agent Controller accepts HTTP traffic. HTTP traffic uses port 5000 for virtual machine installations and port 80 for container-based deployments.

*Example*:\
`true`

***

### `AEMBIT_KERBEROS_ATTESTATION_ENABLED`

[Section titled “AEMBIT\_KERBEROS\_ATTESTATION\_ENABLED”](#aembit_kerberos_attestation_enabled)

Default - not set

OS-All

When `true`, enables Kerberos-based attestation.

**For Linux:** You must set `KRB5_KTNAME` with the Agent Controller keytab path. If you have Kerberos installed, `KRB5_KTNAME` defaults to `/etc/krb5.keytab`.

**For Windows:** Agent Controller inherits Kerberos information from the user it runs as.

*Example*:\
`true`

***

### `AEMBIT_LOG_LEVEL`

[Section titled “AEMBIT\_LOG\_LEVEL”](#aembit_log_level)

Default - `information`

OS-All

Set the Agent Controller log level. The supported levels include `fatal`, `error`, `warning`, `information`, `debug`, `verbose`. The log level value is case insensitive. See [Log level reference](agent-log-level-reference.md#agent-controller-log-levels) for details.

*Example*:\
`verbose`

***

### `AEMBIT_MANAGED_TLS_HOSTNAME`

[Section titled “AEMBIT\_MANAGED\_TLS\_HOSTNAME”](#aembit_managed_tls_hostname)

Default - not set

OS-All

The hostname Agent Proxy uses to connect to the Agent Controller. If set, Aembit uses its own PKI for [Agent Controller TLS](../../user-guide/deploy-install/advanced-options/agent-controller/configure-aembit-pki-agent-controller-tls.md).

This is mutually exclusive with `TLS_PEM_PATH` and `TLS_KEY_PATH`.

*Example*:\
`aembit-agent-controller.example.com`

Note

When you enable TLS on Agent Controller version 1.24.xxxx or later, Agent Controller automatically:

* opens port 443 (5443 on VM) for HTTPS traffic

* doesn’t open port 80 (5000 on VM) for HTTP traffic

As of Agent Controller version 1.29.xxxx, on Kubernetes deployments, Agent Controller exposes Prometheus metrics over HTTP on port 9090 regardless of TLS state. On Virtual Machine deployments, enabling TLS continues to switch Prometheus metrics from HTTP port 9090 to HTTPS port 9091.

***

### `AEMBIT_METRICS_ENABLED`

[Section titled “AEMBIT\_METRICS\_ENABLED”](#aembit_metrics_enabled)

Default - `true`

OS-All

Enable Prometheus metrics. Agent Controller turns this on by default.

*Example*:\
`true`

***

### `AEMBIT_STACK_DOMAIN`

[Section titled “AEMBIT\_STACK\_DOMAIN”](#aembit_stack_domain)

Default - `useast2.aembit.io`

OS-All

The cloud stack to connect to. **Don’t set this value unless directed by your Aembit representative.**

***

### `HTTP_PROXY`

[Section titled “HTTP\_PROXY”](#http_proxy)

Default - not set

OS-All v1.30.3384

Specifies an HTTP proxy for Agent Controller outbound HTTP connections. When set, Agent Controller routes outbound HTTP traffic through the specified proxy. This includes connections to Aembit Cloud for registration and token management. Both `HTTP_PROXY` and `http_proxy` are honored. *Example*:\
`http://proxy.example.com:8080`

***

### `HTTPS_PROXY`

[Section titled “HTTPS\_PROXY”](#https_proxy)

Default - not set

OS-All v1.30.3384

Specifies an HTTPS proxy for Agent Controller outbound HTTPS connections. When set, Agent Controller routes outbound HTTPS traffic through the specified proxy. This includes connections to Aembit Cloud for registration and OAuth. Both `HTTPS_PROXY` and `https_proxy` are honored. *Example*:\
`http://proxy.example.com:8080`

***

Installer doesn’t accept these variables

The Agent Controller installer doesn’t accept `HTTP_PROXY`, `HTTPS_PROXY`, or `NO_PROXY`. To route Agent Controller outbound traffic through an upstream HTTP proxy, set these variables in the Agent Controller service’s environment so that the service inherits them:

* **Windows:** Set them as [system environment variables](https://learn.microsoft.com/en-us/windows/win32/procthread/environment-variables).
* **Linux:** Set them in a [systemd drop-in file](https://www.freedesktop.org/software/systemd/man/latest/systemd.unit.html) using the [`Environment=` directive](https://www.freedesktop.org/software/systemd/man/latest/systemd.exec.html#Environment=).

### `NO_PROXY`

[Section titled “NO\_PROXY”](#no_proxy)

Default - not set

OS-All v1.30.3384

A comma-separated list of hosts or domains that should bypass the proxy. Use this to exclude cloud metadata endpoints and other local services from proxy routing. Both `NO_PROXY` and `no_proxy` are honored.

**Supported patterns:**

Aembit guarantees the following patterns match:

* Specific IP addresses (for example, `169.254.169.254`)
* Specific hostnames (for example, `metadata.google.internal`)

Other patterns may cause unexpected behavior. For more information or help, contact [Aembit Support](https://docs.aembit.io/support-overview).

*Example*:\
`169.254.169.254,fd00:ec2::254,metadata.google.internal`

***

### `SERVICE_LOGON_ACCOUNT`

[Section titled “SERVICE\_LOGON\_ACCOUNT”](#service_logon_account)

Default - not set

OS-Windows

When set, this runs the Agent Controller as a different user which is useful for High Availability deployments. The name you provide must be the fully qualified sAMAccount name.

*Example*:\
`myDomain\MyServiceAccount$`

***

### `TLS_PEM_PATH`

[Section titled “TLS\_PEM\_PATH”](#tls_pem_path)

Default - not set

OS-All

The path to your TLS certificate file. Allows you to specify your own TLS key and certificate to use with [Agent Controller TLS](../../user-guide/deploy-install/advanced-options/agent-controller/configure-aembit-pki-agent-controller-tls.md).

Pair this with `TLS_KEY_PATH`. It’s mutually exclusive with `AEMBIT_MANAGED_TLS_HOSTNAME`.

*Example*:\
`C:\aembit.crt`, `/etc/ssl/certs/aembit.crt`

Note

When you enable TLS on Agent Controller version 1.24.xxxx or later, Agent Controller automatically:

* opens port 443 (5443 on VM) for HTTPS traffic

* doesn’t open port 80 (5000 on VM) for HTTP traffic

As of Agent Controller version 1.29.xxxx, on Kubernetes deployments, Agent Controller exposes Prometheus metrics over HTTP on port 9090 regardless of TLS state. On Virtual Machine deployments, enabling TLS continues to switch Prometheus metrics from HTTP port 9090 to HTTPS port 9091.

***

### `TLS_KEY_PATH`

[Section titled “TLS\_KEY\_PATH”](#tls_key_path)

Default - not set

OS-All

The path to your TLS private key file. Allows you to specify your own TLS key and certificate to use with [Agent Controller TLS](../../user-guide/deploy-install/advanced-options/agent-controller/configure-aembit-pki-agent-controller-tls.md).

Pair this with `TLS_PEM_PATH`. It’s mutually exclusive with `AEMBIT_MANAGED_TLS_HOSTNAME`.

*Example*:\
`C:\aembit.key`, `/etc/ssl/private/.aembit.key`

Note

When you enable TLS on Agent Controller version 1.24.xxxx or later, Agent Controller automatically:

* opens port 443 (5443 on VM) for HTTPS traffic

* doesn’t open port 80 (5000 on VM) for HTTP traffic

As of Agent Controller version 1.29.xxxx, on Kubernetes deployments, Agent Controller exposes Prometheus metrics over HTTP on port 9090 regardless of TLS state. On Virtual Machine deployments, enabling TLS continues to switch Prometheus metrics from HTTP port 9090 to HTTPS port 9091.

***

## Agent Proxy environment variables

[Section titled “Agent Proxy environment variables”](#agent-proxy-environment-variables)

Here is a list of all available environment variables for configuring the Agent Proxy installer:

### `AEMBIT_AGENT_CONTROLLER` Required

[Section titled “AEMBIT\_AGENT\_CONTROLLER ”](#aembit_agent_controller)

Default - not set

OS-All

The location (scheme, host, and port) of the Agent Controller that the Agent Proxy should use.

*Example*:\
`http://agentcontroller.local:5000`

***

### `AEMBIT_AWS_MAX_BUFFERED_PAYLOAD_BYTES` Deprecated

[Section titled “AEMBIT\_AWS\_MAX\_BUFFERED\_PAYLOAD\_BYTES ”](#aembit_aws_max_buffered_payload_bytes)

OS-All

**Deprecated**. This variable has no effect. Previously, this variable set the maximum size in bytes that Agent Proxy buffers when processing AWS S3 uploads with streaming signed payloads. It’s safe to remove from your configuration.

***

### `AEMBIT_CLIENT_WORKLOAD_PROCESS_IDENTIFICATION_ENABLED`

[Section titled “AEMBIT\_CLIENT\_WORKLOAD\_PROCESS\_IDENTIFICATION\_ENABLED”](#aembit_client_workload_process_identification_enabled)

Default - `false`

OS-Linux

Enable process-based Client Workload**Client Workload**: Client Workloads represent software applications, scripts, or automated processes that initiate access requests to Server Workloads, operating autonomously without direct user interaction.[Learn more](../../get-started/concepts/client-workloads.md) identification, including [Process Command Line](../../user-guide/access-policies/client-workloads/identification/process-command-line.md), [Process Name](../../user-guide/access-policies/client-workloads/identification/process-name.md), [Process Path](../../user-guide/access-policies/client-workloads/identification/process-path.md), and [Process User Name](../../user-guide/access-policies/client-workloads/identification/process-user-name.md).

*Example*:\
`false`

***

### `AEMBIT_DEBUG_MAX_CAPTURED_PACKETS_PER_DEVICE`

[Section titled “AEMBIT\_DEBUG\_MAX\_CAPTURED\_PACKETS\_PER\_DEVICE”](#aembit_debug_max_captured_packets_per_device)

Default - not set

OS-Linux

The maximum number of network packets that Agent Proxy monitors per IPv4 network device.

*Example*:\
`2000`

***

### `AEMBIT_DOCKER_CONTAINER_CIDR`

[Section titled “AEMBIT\_DOCKER\_CONTAINER\_CIDR”](#aembit_docker_container_cidr)

Default - not set

OS-Linux

Supports Client Workloads running in Docker Compose on a Virtual Machine. This environment variable specifies the Docker Compose network CIDR that Agent Proxy handles.

*Example*:\
`100.64.0.0/10`

***

### `AEMBIT_ENV_VAR_ALLOWLIST`

[Section titled “AEMBIT\_ENV\_VAR\_ALLOWLIST”](#aembit_env_var_allowlist)

Default - not set (empty)

OS-All v1.31.4764

A comma-separated list of environment variable names that Agent Proxy may capture for use in [dynamic claims](../../user-guide/access-policies/credential-providers/advanced-options/dynamic-claims-oidc.md). By default, the allowlist is empty, so Agent Proxy captures no custom environment variables.

If Aembit Cloud requests an environment variable that’s not on this list, Agent Proxy logs a warning (`requested env variable <name> is not in allow list`) and omits the variable from the response. The credential request still proceeds, but without that claim value.

Allowlist matching is case-sensitive. On Windows, OS-level environment variable lookups are typically case-insensitive, so a process may resolve `my_var` from a system-level `MY_VAR`. Aembit’s allowlist match itself is still case-sensitive and must match the exact case Agent Proxy sees.

For platform-specific guidance on injecting environment variables into Agent Proxy process, see [Configure custom environment variables](../../user-guide/deploy-install/advanced-options/agent-proxy/configure-custom-env-vars.md).

*Example*:\
`CORPORATE_APP_ID,WEBSITE_HOSTNAME,AWS_LAMBDA_FUNCTION_NAME`

***

### `AEMBIT_HTTP_IDLE_TIMEOUT_SECS`

[Section titled “AEMBIT\_HTTP\_IDLE\_TIMEOUT\_SECS”](#aembit_http_idle_timeout_secs)

Default - `3600`

OS-All

Specifies the idle timeout, in seconds, for HTTP/1.1 connections handled by the Agent Proxy. Agent Proxy closes the connection if it doesn’t receive data within the duration set by this environment variable.

*Example*:\
`900`

***

### `AEMBIT_HTTP_SERVER_PORT`

[Section titled “AEMBIT\_HTTP\_SERVER\_PORT”](#aembit_http_server_port)

Default - `8000`

OS-All

Specifies the port the Agent Proxy uses to manage HTTP traffic directed to it via the `http_proxy` and `https_proxy` environment variables. If this port conflicts with any Client Workload ports, it can be overridden with this environment variable.

*Example*:\
`8080`

***

### `AEMBIT_KERBEROS_ATTESTATION_ENABLED`

[Section titled “AEMBIT\_KERBEROS\_ATTESTATION\_ENABLED”](#aembit_kerberos_attestation_enabled-1)

Default - not set

OS-Linux

Enable Kerberos-based attestation. This value isn’t set by default. To enable it, set this value to true.

*Example*:\
`true`

***

### `AEMBIT_LOG_LEVEL` (Replaces `AEMBIT_LOG`)

[Section titled “AEMBIT\_LOG\_LEVEL (Replaces AEMBIT\_LOG)”](#aembit_log_level-replaces-aembit_log)

Default - `info`

OS-All

Set the Agent Proxy log level. The supported levels include `error`, `warn`, `info`, `debug`, `trace`, `off`. The log level value is case insensitive. See [Log level reference](agent-log-level-reference.md#agent-proxy-log-levels) for details.

*Example*:\
`debug`

***

### `AEMBIT_METRICS_ENABLED`

[Section titled “AEMBIT\_METRICS\_ENABLED”](#aembit_metrics_enabled-1)

Default - `true`

OS-All

Enable Prometheus metrics. Defaults to `true`.

*Example*:\
`true`

***

### `AEMBIT_METRICS_PORT`

[Section titled “AEMBIT\_METRICS\_PORT”](#aembit_metrics_port)

Default - `9099`

OS-All

The port where Agent Proxy exposes Prometheus metrics.

*Example*:\
`9099`

***

### `AEMBIT_PASS_THROUGH_TRAFFIC_BEFORE_REGISTRATION`

[Section titled “AEMBIT\_PASS\_THROUGH\_TRAFFIC\_BEFORE\_REGISTRATION”](#aembit_pass_through_traffic_before_registration)

Default - `true`

OS-All

When set to true, Agent Proxy operates in Passthrough mode. Connections proceed without credential injection until Aembit Cloud**Aembit Cloud**: Aembit Cloud serves as both the central control plane and management plane, making authorization decisions, evaluating policies, coordinating credential issuance, and providing administrative interfaces for configuration.[Learn more](../../get-started/concepts/aembit-cloud.md) registration completes. When set to false, incoming Client Workloads can’t connect until after registration completes. On Kubernetes this has the effect of [delaying pod startup](../../user-guide/deploy-install/kubernetes/kubernetes.md#delaying-pod-startup-until-agent-proxy-has-registered).

*Example*:\
`false`

***

### `AEMBIT_POST_START_MAX_WAIT_SEC` Kubernetes only

[Section titled “AEMBIT\_POST\_START\_MAX\_WAIT\_SEC ”](#aembit_post_start_max_wait_sec)

Default - `60`

OS-All

The maximum number of seconds you permit the Agent Proxy `postStart` lifecycle hook to run before failing Client Workload pod deployment.

See [Delaying pod startup until the Agent Proxy has registered](../../user-guide/deploy-install/kubernetes/kubernetes.md#delaying-pod-startup-until-agent-proxy-has-registered).

*Example*:\
`100`

***

### `AEMBIT_PRIVILEGED_KEYTAB`

[Section titled “AEMBIT\_PRIVILEGED\_KEYTAB”](#aembit_privileged_keytab)

Default - `false`

OS-Linux

Set the configuration flag to enable the Agent Proxy to access a Kerberos principal located in a keytab file with root-only read permissions. Mandatory if `AEMBIT_KERBEROS_ATTESTATION_ENABLED` is enabled.

*Example*:\
`true`

***

### `AEMBIT_RESOURCE_SET_ID`

[Section titled “AEMBIT\_RESOURCE\_SET\_ID”](#aembit_resource_set_id)

Default - not set

OS-All

Associates Agent Proxy with a specific [Resource Set](../../user-guide/administration/resource-sets/overview.md).

*Example*:\
`de48ebc2-3587-4cc6-823b-46434991e896`

***

### `AEMBIT_SIGTERM_STRATEGY`

[Section titled “AEMBIT\_SIGTERM\_STRATEGY”](#aembit_sigterm_strategy)

Default - `immediate`

OS-Linux

The strategy used by Agent Proxy to handle the `SIGTERM` signal. Supported values are `immediate`, which exits immediately, and `sigkill`, which ignores the `SIGTERM` signal and waits for a `SIGKILL`. For details on configuring the `AEMBIT_SIGTERM_STRATEGY` environment variable and termination strategies, see [Agent Proxy Termination Strategy](../../user-guide/deploy-install/advanced-options/agent-proxy/agent-proxy-termination-strategy.md).

*Example*:\
`sigkill`

***

### `AEMBIT_STEERING_ALLOWED_HOSTS`

[Section titled “AEMBIT\_STEERING\_ALLOWED\_HOSTS”](#aembit_steering_allowed_hosts)

Default - not set

OS-Linux

A list of comma-separated hostnames for which Agent Proxy should proxy traffic.

*Example*:\
`graph.microsoft.com,google.com`

***

### `AEMBIT_TENANT_GRPC_PING_INTERVAL_SECS`

[Section titled “AEMBIT\_TENANT\_GRPC\_PING\_INTERVAL\_SECS”](#aembit_tenant_grpc_ping_interval_secs)

Default - not set

OS-All v1.32.4999

Sets how often, in seconds, Agent Proxy sends gRPC keep-alive messages over its connection to your Aembit Tenant. This setting is off by default. During normal operation, Agent Proxy already exchanges messages over this connection every 60 seconds.

Set this only for unconventional networks, such as a Secure Web Gateway (SWG) that drops or stalls an idle connection without closing it. In those cases, the keep-alive messages let Agent Proxy detect a dead connection and reconnect sooner. Accepts any whole number of seconds greater than 0. Use together with `AEMBIT_TENANT_GRPC_PING_TIMEOUT_SECS`.

*Example*:\
`30`

***

### `AEMBIT_TENANT_GRPC_PING_TIMEOUT_SECS`

[Section titled “AEMBIT\_TENANT\_GRPC\_PING\_TIMEOUT\_SECS”](#aembit_tenant_grpc_ping_timeout_secs)

Default - not set

OS-All v1.32.4999

Sets the maximum time, in seconds, that Agent Proxy waits for a reply to a gRPC keep-alive message. If no reply arrives within this time, Agent Proxy treats the connection to your Aembit Tenant as dead and reconnects. This setting is off by default.

Set this together with `AEMBIT_TENANT_GRPC_PING_INTERVAL_SECS` for unconventional networks where a connection can stall without closing. Accepts any whole number of seconds greater than 0.

*Example*:\
`10`

***

### `AGENT_TRUST_PATH`

[Section titled “AGENT\_TRUST\_PATH”](#agent_trust_path)

Default - not set

OS-All

The path to a PEM-encoded CA certificate or trust bundle that the Agent Proxy trusts when establishing its TLS connection to the Aembit Cloud.

Set this when the Agent Proxy’s connection to the Aembit Cloud presents a certificate signed by a private or internal certificate authority. This can happen when an inspecting proxy terminates TLS on the Agent Proxy’s outbound traffic. Without the issuing CA in its trust store, the Agent Proxy rejects the connection with an `invalid peer certificate: UnknownIssuer` error. When this variable isn’t set, the Agent Proxy uses its default system trust store.

*Example*:\
`/etc/aembit/cloud-ca.pem`, `C:\aembit\cloud-ca.pem`

***

### `CLIENT_WORKLOAD_ID`

[Section titled “CLIENT\_WORKLOAD\_ID”](#client_workload_id)

Default - not set

OS-All

Associate Agent Proxy with the specified Client Workload Id. Aembit uses this in conjunction with [Aembit Client Id](../../user-guide/access-policies/client-workloads/identification/aembit-client-id.md) configuration.

*Example*:\
`7e75e718-7634-480b-9f7b-a07bb5a4f11d`

***

### `HTTP_PROXY`

[Section titled “HTTP\_PROXY”](#http_proxy-1)

Default - not set

OS-All v1.31.4670

Specifies an upstream HTTP proxy for Agent Proxy outbound HTTP connections. When set, Agent Proxy routes outbound HTTP traffic through the specified proxy. Both `HTTP_PROXY` and `http_proxy` are honored on Linux via `reqwest`’s system-proxy support.

*Example*:\
`http://proxy.example.com:8080`

***

### `HTTPS_PROXY`

[Section titled “HTTPS\_PROXY”](#https_proxy-1)

Default - not set

OS-All v1.31.4670

Specifies an upstream HTTPS proxy for Agent Proxy outbound HTTPS connections. When set, Agent Proxy routes outbound HTTPS traffic through the specified proxy. Both `HTTPS_PROXY` and `https_proxy` are honored on Linux via `reqwest`’s system-proxy support.

Agent Proxy supports only the `http` scheme for the proxy URL. For example, `HTTPS_PROXY=https://proxy.example.com:8080` is not valid.

*Example*:\
`http://proxy.example.com:8080`

***

### `K8S_NAMESPACE`

[Section titled “K8S\_NAMESPACE”](#k8s_namespace)

Default - not set

Kubernetes only

The Kubernetes namespace where Agent Proxy runs.

The Aembit Helm chart sets this automatically via the Kubernetes downward API (`fieldRef: metadata.namespace` in the chart’s webhook template).

When set, this variable is always available for use in [dynamic claims](../../user-guide/access-policies/credential-providers/advanced-options/dynamic-claims-oidc.md), regardless of whether it appears in `AEMBIT_ENV_VAR_ALLOWLIST`.

*Example*:\
`my-namespace`

***

### `K8S_POD_NAME`

[Section titled “K8S\_POD\_NAME”](#k8s_pod_name)

Default - not set

Kubernetes only

The Kubernetes pod name where Agent Proxy runs.

The Aembit Helm chart sets this automatically via the Kubernetes downward API (`fieldRef: metadata.name` in the chart’s webhook template).

When set, this variable is always available for use in [dynamic claims](../../user-guide/access-policies/credential-providers/advanced-options/dynamic-claims-oidc.md), regardless of whether it appears in `AEMBIT_ENV_VAR_ALLOWLIST`.

*Example*:\
`my-app-pod-12345`

***

### `KUBERNETES_PROVIDER_ID`

[Section titled “KUBERNETES\_PROVIDER\_ID”](#kubernetes_provider_id)

Default - not set

Kubernetes only

A customer-defined identifier for the Kubernetes cluster where Agent Proxy runs. Used by integrations such as the [Wiz access conditions integration](../../user-guide/access-policies/access-conditions/integrations/wiz.md) and available for use in [dynamic claims](../../user-guide/access-policies/credential-providers/advanced-options/dynamic-claims-oidc.md), regardless of whether it appears in `AEMBIT_ENV_VAR_ALLOWLIST`.

Set this value through your Aembit Helm chart deployment by setting `agentProxy.env.KUBERNETES_PROVIDER_ID` to your cluster identifier.

*Example*:\
`arn:aws:eks:us-east-2:111122223333:cluster/my-cluster`

***

Installer doesn’t accept these variables

The Agent Proxy installer doesn’t accept `HTTP_PROXY`, `HTTPS_PROXY`, or `NO_PROXY`. To route Agent Proxy outbound traffic through an upstream HTTP proxy, set these variables in the Agent Proxy service’s environment so that the service inherits them:

* **Windows:** Set them as [system environment variables](https://learn.microsoft.com/en-us/windows/win32/procthread/environment-variables). For required `NO_PROXY` entries on Windows Server, see [Install Agent Proxy on Windows Server](../../user-guide/deploy-install/virtual-machine/windows/agent-proxy-install-windows.md).
* **Linux:** Set them in a [systemd drop-in file](https://www.freedesktop.org/software/systemd/man/latest/systemd.unit.html) using the [`Environment=` directive](https://www.freedesktop.org/software/systemd/man/latest/systemd.exec.html#Environment=).

### `NO_PROXY`

[Section titled “NO\_PROXY”](#no_proxy-1)

Default - not set

OS-All v1.31.4670

A comma-separated list of hosts or domains that should bypass the upstream proxy. Use this to exclude the Agent Controller, cloud metadata endpoints, and other local services from proxy routing. Both `NO_PROXY` and `no_proxy` are honored on Linux via `reqwest`’s system-proxy support.

The `NO_PROXY` environment variable must contain all the following entries so that Agent Proxy routes its traffic correctly. Aembit uses Instance Metadata Service (IMDS) addresses to attest workloads on AWS, Azure, and GCP.

| Entry                      | Purpose                               |
| -------------------------- | ------------------------------------- |
| `169.254.169.254`          | AWS, Azure, and GCP instance metadata |
| `fd00:ec2::254`            | AWS instance metadata via IPv6        |
| `metadata.google.internal` | GCP instance metadata                 |
| `fd20:ce::254`             | GCP instance metadata via IPv6        |

You may need to include additional hosts, depending on your deployment.

**Supported patterns:**

Aembit guarantees the following patterns match:

* Specific IP addresses (for example, `169.254.169.254`)
* Specific hostnames (for example, `metadata.google.internal`)

Other patterns may cause unexpected behavior. For more information or help, contact [Aembit Support](https://docs.aembit.io/support-overview).

*Example*:\
`aembit.io,169.254.169.254,fd00:ec2::254,metadata.google.internal`

## Agent Injector environment variables

[Section titled “Agent Injector environment variables”](#agent-injector-environment-variables)

### `AEMBIT_LOG_LEVEL` (Replaces `AEMBIT_LOG`)

[Section titled “AEMBIT\_LOG\_LEVEL (Replaces AEMBIT\_LOG)”](#aembit_log_level-replaces-aembit_log-1)

Default - `info`

OS-All

Set the Agent Injector log level. The supported levels include `error`, `warn`, `info` (default value), `debug`, `trace`, and `off`. See [Log level reference](agent-log-level-reference.md) for details.

*Example*:\
`warn`

## Aembit CLI environment variables

[Section titled “Aembit CLI environment variables”](#aembit-cli-environment-variables)

Here is a list of all available environment variables for configuring the [Aembit CLI](../../cli-guide/overview.md):

Environment variable and command option priority

You can configure the Aembit CLI using both environment variables and command options. Command options take precedence, overriding any corresponding environment variables.

For example, if you’ve set a value with the `--client-id` option, Aembit CLI uses that over the `AEMBIT_CLIENT_ID` environment variable. This lets you establish a default configuration with environment variables and override specific settings for individual commands as needed.

### `AEMBIT_CLIENT_ID` Required

[Section titled “AEMBIT\_CLIENT\_ID ”](#aembit_client_id)

Default - not set

OS-All

This value represents the Edge SDK Client ID from your Aembit Trust Provider. Aembit automatically generates the Edge SDK Client ID when you configure a Trust Provider in your Aembit Tenant UI. To retrieve your Edge SDK Client ID, see [Find your Edge SDK Client ID](../../user-guide/access-policies/trust-providers/get-edge-sdk-client-id.md).

*Example*:\
`aembit:useast2:a12bc3:identity:github_idtoken:63ab7be6-9785-4a14-be1c-2acf0253070b`

***

### `AEMBIT_CLIENT_TLS_PRIVATE_KEY`

[Section titled “AEMBIT\_CLIENT\_TLS\_PRIVATE\_KEY”](#aembit_client_tls_private_key)

Default - not set

OS-All v1.32

The path to a PEM-encoded PKCS #8 private key file used to retrieve an X.509-SVID certificate. The certificate comes from an [X.509-SVID Credential Provider](../../user-guide/access-policies/credential-providers/about-spiffe-x509-svid.md). Aembit CLI generates a Certificate Signing Request (CSR) from the supplied key, submits it through the credential retrieval flow, and returns the signed certificate chain in `CLIENT_CERT_CHAIN`. The private key never leaves the local machine.

Equivalent to the [`--client-tls-private-key`](../../cli-guide/reference/credentials-get.md#--client-tls-private-key) flag on `aembit credentials get`.

*Example*:\
`/etc/aembit/client.key`

***

### `AEMBIT_ENV_VAR_ALLOWLIST`

[Section titled “AEMBIT\_ENV\_VAR\_ALLOWLIST”](#aembit_env_var_allowlist-1)

Default - not set (empty)

OS-All v1.31.4764

A comma-separated list of environment variable names that Aembit CLI may capture for use in [dynamic claims](../../user-guide/access-policies/credential-providers/advanced-options/dynamic-claims-oidc.md). By default, the allowlist is empty, so Aembit CLI captures no custom environment variables.

The CLI inherits the shell environment automatically; you only need to set the allowlist itself explicitly.

*Example*:\
`CORPORATE_APP_ID,WEBSITE_HOSTNAME`

***

### `AEMBIT_LOG_LEVEL`

[Section titled “AEMBIT\_LOG\_LEVEL”](#aembit_log_level-1)

Default - `warn`

OS-All

The log level to use for the Aembit CLI. This controls the verbosity of the output from the CLI. The supported levels include `off`, `trace`, `debug`, `info`, `warn`, `error`.

*Example*:\
`debug`

***

### `AEMBIT_RESOURCE_SET_ID`

[Section titled “AEMBIT\_RESOURCE\_SET\_ID”](#aembit_resource_set_id-1)

Default - not set

OS-All

The [Resource Set](../../user-guide/administration/resource-sets/overview.md) to authenticate against and within which the Access Policy**Access Policy**: Access Policies define, enforce, and audit access between Client and Server Workloads by cryptographically verifying workload identity and contextual factors rather than relying on static secrets.[Learn more](../../get-started/concepts/access-policies.md) matching happens. This is useful for when you want to use a specific Resource Set for your credentials. You can find the Resource Set ID in your Aembit Tenant UI under the Resource Sets section.

*Example*:\
`78bg7be6-9301-hj14-d51c-2acf02530y67`
