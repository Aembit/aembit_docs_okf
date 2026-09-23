---
type: reference
title: "Edge Component environment variables reference"
description: "Reference for environment variables of Edge Components categorized by deployment type"
resource: https://docs.aembit.io/reference/edge-components/edge-component-env-vars/
tags: ["edge-component"]
timestamp: 2026-09-22T11:51:31-07:00
---

# Edge Component environment variables reference

The following sections list and describe the environment variables available for Edge Components:

* [Agent Controller](#agent-controller-environment-variables)

* [Agent Proxy](#agent-proxy-environment-variables)

* [Agent Injector](#agent-injector-environment-variables)

* [Aembit CLI](#aembit-cli-environment-variables)

To see how the proxy, trust, and keep-alive variables fit together on a network fronted by a Secure Web Gateway, see [Running Edge Components behind TLS-inspecting proxies](../../user-guide/deploy-install/advanced-options/running-behind-secure-web-gateways.md).

## Agent Controller environment variables

Here is a list of all available environment variables for configuring the Agent Controller installer:

### `AEMBIT_AGENT_CONTROLLER_ID` Required

Default - not set

OS-All

Required if not using `AEMBIT_DEVICE_CODE`.

The Agent Controller ID, available in your tenant’s administrative console for each Agent Controller. This ID is utilized for Trust Provider registration. You must provide either this or the `AEMBIT_DEVICE_CODE` environment variable.

*Example*:\
`01234567-89ab-cdef-0123-456789abcdef`

***

### `AEMBIT_DEVICE_CODE` Required

Default - not set

OS-All

Required if not using `AEMBIT_AGENT_CONTROLLER_ID`.

The device code for the Agent Controller. Generate this code in your tenant’s administrative console for code-based registration. You must provide either this or the `AEMBIT_AGENT_CONTROLLER_ID` environment variable.

*Example*:\
`123456`

***

### `AEMBIT_TENANT_ID` Required

Default - not set

OS-All

The Aembit Tenant ID that the Agent Controller will register with.

*Example*:\
`123abc`

***

### `AEMBIT_HTTP_PORT_DISABLED`

Default - `false`

OS-All

When `true`, turns off HTTP support in Agent Controller, restricting communication to HTTPS only. When `false`, Agent Controller accepts HTTP traffic. HTTP traffic uses port 5000 for virtual machine installations and port 80 for container-based deployments.

*Example*:\
`true`

***

### `AEMBIT_KERBEROS_ATTESTATION_ENABLED`

Default - not set

OS-All

When `true`, enables Kerberos-based attestation.

**For Linux:** Set `KRB5_KTNAME` to the Agent Controller keytab path. When you don’t set it, Kerberos uses `/etc/krb5.keytab`.

**For Windows:** Agent Controller uses the credentials of the account the service runs as, `LocalService` by default.

*Example*:\
`true`

***

### `AEMBIT_LOG_LEVEL`

Default - `information`

OS-All

Set the Agent Controller log level. The supported levels include `fatal`, `error`, `warning`, `information`, `debug`, `verbose`. The log level value is case insensitive. See [Log level reference](agent-log-level-reference.md#agent-controller-log-levels) for details.

*Example*:\
`verbose`

***

### `AEMBIT_MANAGED_TLS_HOSTNAME`

Default - not set

OS-All

The hostname Agent Proxy uses to connect to the Agent Controller. If set, Aembit uses its own PKI for [Agent Controller TLS](../../user-guide/deploy-install/advanced-options/agent-controller/configure-aembit-pki-agent-controller-tls.md).

This is mutually exclusive with `TLS_PEM_PATH` and `TLS_KEY_PATH`.

*Example*:\
`aembit-agent-controller.example.com`

> **Note**
>
> When you enable TLS on Agent Controller version 1.24.xxxx or later, Agent Controller automatically:
>
> * opens port 443 (5443 on VM) for HTTPS traffic
>
> * doesn’t open port 80 (5000 on VM) for HTTP traffic
>
> As of Agent Controller version 1.29.xxxx, on Kubernetes deployments, Agent Controller exposes Prometheus metrics over HTTP on port 9090 regardless of TLS state. On Virtual Machine deployments, enabling TLS continues to switch Prometheus metrics from HTTP port 9090 to HTTPS port 9091.

***

### `AEMBIT_METRICS_ENABLED`

Default - `true`

OS-All

Enable Prometheus metrics. Agent Controller turns this on by default.

*Example*:\
`true`

***

### `AEMBIT_STACK_DOMAIN`

Default - `useast2.aembit.io`

OS-All

The cloud stack to connect to. **Don’t set this value unless directed by your Aembit representative.**

***

### `HTTP_PROXY`

Default - not set

OS-All v1.30.3384

Specifies an HTTP proxy for Agent Controller outbound HTTP connections. When set, Agent Controller routes outbound HTTP traffic through the specified proxy. This includes connections to Aembit Cloud for registration and token management. Both `HTTP_PROXY` and `http_proxy` are honored. *Example*:\
`http://proxy.example.com:8080`

***

### `HTTPS_PROXY`

Default - not set

OS-All v1.30.3384

Specifies an HTTPS proxy for Agent Controller outbound HTTPS connections. When set, Agent Controller routes outbound HTTPS traffic through the specified proxy. This includes connections to Aembit Cloud for registration and OAuth. Both `HTTPS_PROXY` and `https_proxy` are honored. *Example*:\
`http://proxy.example.com:8080`

***

> **Installer doesn’t accept these variables**
>
> The Agent Controller installer doesn’t accept `HTTP_PROXY`, `HTTPS_PROXY`, or `NO_PROXY`. To route Agent Controller outbound traffic through an upstream HTTP proxy, set these variables in the Agent Controller service’s environment so that the service inherits them:
>
> * **Windows:** Set them as [system environment variables](https://learn.microsoft.com/en-us/windows/win32/procthread/environment-variables).
> * **Linux:** Set them in a [systemd drop-in file](https://www.freedesktop.org/software/systemd/man/latest/systemd.unit.html) using the [`Environment=` directive](https://www.freedesktop.org/software/systemd/man/latest/systemd.exec.html#Environment=).

### `NO_PROXY`

Default - not set

OS-All v1.30.3384

A comma-separated list of hosts or domains that should bypass the proxy. Use this to exclude cloud metadata endpoints and other local services from proxy routing. Both `NO_PROXY` and `no_proxy` are honored.

**Supported patterns:**

Aembit guarantees the following patterns match:

* Specific IP addresses (for example, `169.254.169.254`)
* Specific hostnames (for example, `metadata.google.internal`)

Other patterns may cause unexpected behavior. For more information or help, contact [Aembit Support](../../support-overview.md).

*Example*:\
`169.254.169.254,metadata.google.internal`

***

### `SERVICE_LOGON_ACCOUNT`

Default - not set

OS-Windows

Runs the Agent Controller service as the named account instead of `LocalService`. Use it for high availability deployments that share a Group Managed Service Account (gMSA) across Agent Controller hosts. Provide the account in Down-Level Logon Name format, `<NetBIOS domain name>\<sAMAccountName>$`. The installer accepts a built-in account or a gMSA. It can’t run the service as a domain user that requires a password.

*Example*:\
`myDomain\MyServiceAccount$`

***

### `TLS_PEM_PATH`

Default - not set

OS-All

The path to your TLS certificate file. Allows you to specify your own TLS key and certificate to use with [Agent Controller TLS](../../user-guide/deploy-install/advanced-options/agent-controller/configure-aembit-pki-agent-controller-tls.md).

Pair this with `TLS_KEY_PATH`. It’s mutually exclusive with `AEMBIT_MANAGED_TLS_HOSTNAME`.

*Example*:\
`C:\aembit.crt`, `/etc/ssl/certs/aembit.crt`

> **Note**
>
> When you enable TLS on Agent Controller version 1.24.xxxx or later, Agent Controller automatically:
>
> * opens port 443 (5443 on VM) for HTTPS traffic
>
> * doesn’t open port 80 (5000 on VM) for HTTP traffic
>
> As of Agent Controller version 1.29.xxxx, on Kubernetes deployments, Agent Controller exposes Prometheus metrics over HTTP on port 9090 regardless of TLS state. On Virtual Machine deployments, enabling TLS continues to switch Prometheus metrics from HTTP port 9090 to HTTPS port 9091.

***

### `TLS_KEY_PATH`

Default - not set

OS-All

The path to your TLS private key file. Allows you to specify your own TLS key and certificate to use with [Agent Controller TLS](../../user-guide/deploy-install/advanced-options/agent-controller/configure-aembit-pki-agent-controller-tls.md).

Pair this with `TLS_PEM_PATH`. It’s mutually exclusive with `AEMBIT_MANAGED_TLS_HOSTNAME`.

*Example*:\
`C:\aembit.key`, `/etc/ssl/private/.aembit.key`

> **Note**
>
> When you enable TLS on Agent Controller version 1.24.xxxx or later, Agent Controller automatically:
>
> * opens port 443 (5443 on VM) for HTTPS traffic
>
> * doesn’t open port 80 (5000 on VM) for HTTP traffic
>
> As of Agent Controller version 1.29.xxxx, on Kubernetes deployments, Agent Controller exposes Prometheus metrics over HTTP on port 9090 regardless of TLS state. On Virtual Machine deployments, enabling TLS continues to switch Prometheus metrics from HTTP port 9090 to HTTPS port 9091.

***

## Agent Proxy environment variables

Here is a list of all available environment variables for configuring the Agent Proxy installer:

### `AEMBIT_AGENT_CONTROLLER` Required

Default - not set

OS-All

The location (scheme, host, and port) of the Agent Controller that the Agent Proxy should use. With Kerberos attestation enabled, the host must be the fully qualified domain name (FQDN) carried by the Agent Controller’s Service Principal Name (SPN).

*Example*:\
`http://agentcontroller.local:5000`

***

### `AEMBIT_AGENT_PROXY_KERBEROS_PRINCIPAL`

Default - not set

OS-Linux

The Kerberos principal Agent Proxy authenticates as when `AEMBIT_KERBEROS_ATTESTATION_ENABLED` is `true`. Agent Proxy reads the principal’s key from the keytab named by `KRB5_CLIENT_KTNAME` or `KRB5_KTNAME`. The `aembit_agent_proxy` Linux user must be able to read that keytab. The default, `/etc/krb5.keytab`, is root-only on most hosts, so either name a keytab you’ve made readable or set `AEMBIT_PRIVILEGED_KEYTAB=true` instead of this variable. When `AEMBIT_PRIVILEGED_KEYTAB` is `true`, the installer sets this variable from the host keytab and you don’t need to.

*Example*:\
`webapp01$@EXAMPLE.COM`

***

### `AEMBIT_AWS_MAX_BUFFERED_PAYLOAD_BYTES` Deprecated

OS-All

**Deprecated**. This variable has no effect. Previously, this variable set the maximum size in bytes that Agent Proxy buffers when processing AWS S3 uploads with streaming signed payloads. It’s safe to remove from your configuration.

***

### `AEMBIT_CLIENT_WORKLOAD_PROCESS_IDENTIFICATION_ENABLED`

Default - `false`

OS-Linux

Enable process-based Client Workload identification, including [Process Command Line](../../user-guide/access-policies/client-workloads/identification/process-command-line.md), [Process Name](../../user-guide/access-policies/client-workloads/identification/process-name.md), [Process Path](../../user-guide/access-policies/client-workloads/identification/process-path.md), and [Process User Name](../../user-guide/access-policies/client-workloads/identification/process-user-name.md).

*Example*:\
`false`

***

### `AEMBIT_DEBUG_MAX_CAPTURED_PACKETS_PER_DEVICE`

Default - not set

OS-Linux

The maximum number of network packets that Agent Proxy monitors per IPv4 network device.

*Example*:\
`2000`

***

### `AEMBIT_DOCKER_CONTAINER_CIDR`

Default - not set

OS-Linux

Supports Client Workloads running in Docker Compose on a Virtual Machine. This environment variable specifies the Docker Compose network CIDR that Agent Proxy handles.

*Example*:\
`100.64.0.0/10`

***

### `AEMBIT_ENV_VAR_ALLOWLIST`

Default - not set (empty)

OS-All v1.31.4764

A comma-separated list of environment variable names that Agent Proxy may capture for use in [dynamic claims](../../user-guide/access-policies/credential-providers/advanced-options/dynamic-claims.md). By default, the allowlist is empty, so Agent Proxy captures no custom environment variables.

If Aembit Cloud requests an environment variable that’s not on this list, Agent Proxy logs a warning (`requested env variable <name> is not in allow list`) and omits the variable from the response. The credential request still proceeds, but without that claim value.

Allowlist matching is case-sensitive. On Windows, OS-level environment variable lookups are typically case-insensitive, so a process may resolve `my_var` from a system-level `MY_VAR`. Aembit’s allowlist match itself is still case-sensitive and must match the exact case Agent Proxy sees.

For platform-specific guidance on injecting environment variables into Agent Proxy process, see [Configure custom environment variables](../../user-guide/deploy-install/advanced-options/agent-proxy/configure-custom-env-vars.md).

*Example*:\
`CORPORATE_APP_ID,WEBSITE_HOSTNAME,AWS_LAMBDA_FUNCTION_NAME`

***

### `AEMBIT_HTTP_IDLE_TIMEOUT_SECS`

Default - `3600`

OS-All

Specifies the idle timeout, in seconds, for HTTP/1.1 connections handled by the Agent Proxy. Agent Proxy closes the connection if it doesn’t receive data within the duration set by this environment variable.

*Example*:\
`900`

***

### `AEMBIT_HTTP_SERVER_PORT`

Default - `8000`

OS-All

Specifies the port the Agent Proxy uses to manage HTTP traffic directed to it via the `http_proxy` and `https_proxy` environment variables. If this port conflicts with any Client Workload ports, it can be overridden with this environment variable.

*Example*:\
`8080`

***

### `AEMBIT_KERBEROS_ATTESTATION_ENABLED`

Default - not set

OS-All

When `true`, enables Kerberos-based attestation through the [Kerberos Trust Provider](../../user-guide/access-policies/trust-providers/kerberos-trust-provider.md).

**For Linux:** Also set `AEMBIT_PRIVILEGED_KEYTAB` or `AEMBIT_AGENT_PROXY_KERBEROS_PRINCIPAL`.

**For Windows:** You don’t need any other variable. Agent Proxy authenticates as the host’s computer account.

*Example*:\
`true`

***

### `AEMBIT_LOG_LEVEL` (Replaces `AEMBIT_LOG`)

Default - `info`

OS-All

Set the Agent Proxy log level. The supported levels include `error`, `warn`, `info`, `debug`, `trace`, `off`. The log level value is case insensitive. See [Log level reference](agent-log-level-reference.md#agent-proxy-log-levels) for details.

*Example*:\
`debug`

***

### `AEMBIT_METRICS_ENABLED`

Default - `true`

OS-All

Enable Prometheus metrics. Defaults to `true`.

*Example*:\
`true`

***

### `AEMBIT_METRICS_PORT`

Default - `9099`

OS-All

The port where Agent Proxy exposes Prometheus metrics.

*Example*:\
`9099`

***

### `AEMBIT_PASS_THROUGH_TRAFFIC_BEFORE_REGISTRATION`

Default - `true`

OS-All

When set to true, Agent Proxy operates in Passthrough mode. Connections proceed without credential injection until Aembit Cloud registration completes. When set to false, incoming Client Workloads can’t connect until after registration completes. On Kubernetes this has the effect of [delaying pod startup](../../user-guide/deploy-install/kubernetes/kubernetes.md#delaying-pod-startup-until-agent-proxy-has-registered).

*Example*:\
`false`

***

### `AEMBIT_POST_START_MAX_WAIT_SEC` Kubernetes only

Default - `60`

OS-All

The maximum number of seconds you permit the Agent Proxy `postStart` lifecycle hook to run before failing Client Workload pod deployment.

See [Delaying pod startup until the Agent Proxy has registered](../../user-guide/deploy-install/kubernetes/kubernetes.md#delaying-pod-startup-until-agent-proxy-has-registered).

*Example*:\
`100`

***

### `AEMBIT_PRIVILEGED_KEYTAB`

Default - `false`

OS-Linux

When `true`, lets Agent Proxy authenticate as the computer account principal in `/etc/krb5.keytab` while that file keeps root-only read permissions. The installer grants the Agent Proxy read access through `sudo` and records the principal it finds.

When `AEMBIT_KERBEROS_ATTESTATION_ENABLED` is `true` on Linux, set either this variable or `AEMBIT_AGENT_PROXY_KERBEROS_PRINCIPAL`. Don’t combine this variable with `KRB5_KTNAME` or `KRB5_CLIENT_KTNAME`; it reads `/etc/krb5.keytab` only. Windows ignores this variable.

*Example*:\
`true`

***

### `AEMBIT_RESOURCE_SET_ID`

Default - not set

OS-All

Associates Agent Proxy with a specific [Resource Set](../../user-guide/administration/resource-sets/overview.md).

*Example*:\
`de48ebc2-3587-4cc6-823b-46434991e896`

***

### `AEMBIT_SIGTERM_STRATEGY`

Default - `immediate`

OS-Linux

The strategy used by Agent Proxy to handle the `SIGTERM` signal. Supported values are `immediate`, which exits immediately, and `sigkill`, which ignores the `SIGTERM` signal and waits for a `SIGKILL`. For details on configuring the `AEMBIT_SIGTERM_STRATEGY` environment variable and termination strategies, see [Agent Proxy Termination Strategy](../../user-guide/deploy-install/advanced-options/agent-proxy/agent-proxy-termination-strategy.md).

*Example*:\
`sigkill`

***

### `AEMBIT_STEERING_ALLOWED_HOSTS`

Default - not set

OS-Linux

A list of comma-separated hostnames for which Agent Proxy should proxy traffic.

*Example*:\
`graph.microsoft.com,google.com`

***

### `AEMBIT_TENANT_GRPC_PING_INTERVAL_SECS`

Default - not set

OS-All v1.34.5755

Sets how often, in seconds, Agent Proxy sends gRPC keep-alive messages over its connection to your Aembit Tenant. This setting is off by default. During normal operation, Agent Proxy already exchanges messages over this connection every 60 seconds.

Set this only for unconventional networks, such as a Secure Web Gateway (SWG) that drops or stalls an idle connection without closing it. In those cases, the keep-alive messages let Agent Proxy detect a dead connection and reconnect sooner. Accepts any whole number of seconds greater than 0. Use together with `AEMBIT_TENANT_GRPC_PING_TIMEOUT_SECS`. Set the interval shorter than the gateway’s idle timeout, as described in [Running Edge Components behind TLS-inspecting proxies](../../user-guide/deploy-install/advanced-options/running-behind-secure-web-gateways.md).

Linux honors this variable from Agent Proxy 1.32.4999 on. The Windows installer applies it as an MSI property from Agent Proxy 1.34.5755 on.

*Example*:\
`30`

***

### `AEMBIT_TENANT_GRPC_PING_TIMEOUT_SECS`

Default - not set

OS-All v1.34.5755

Sets the maximum time, in seconds, that Agent Proxy waits for a reply to a gRPC keep-alive message. If no reply arrives within this time, Agent Proxy treats the connection to your Aembit Tenant as dead and reconnects. This setting is off by default.

Set this together with `AEMBIT_TENANT_GRPC_PING_INTERVAL_SECS` for unconventional networks where a connection can stall without closing. Accepts any whole number of seconds greater than 0.

Linux honors this variable from Agent Proxy 1.32.4999 on. The Windows installer applies it as an MSI property from Agent Proxy 1.34.5755 on.

*Example*:\
`10`

***

### `AGENT_TRUST_PATH`

Default - not set

OS-All

The path to a file holding a single PEM-encoded CA certificate that the Agent Proxy trusts when establishing its TLS connection to the Aembit Cloud.

Set this when the Agent Proxy’s connection to the Aembit Cloud presents a certificate signed by a private or internal certificate authority. This can happen when an inspecting proxy terminates TLS on the Agent Proxy’s outbound traffic. Without the issuing CA in its trust store, the Agent Proxy rejects the connection with an `invalid peer certificate: UnknownIssuer` error. When this variable isn’t set, the Agent Proxy uses its default system trust store.

Agent Proxy reads only the first certificate in the file. Point this variable at a file holding the inspecting proxy’s root CA certificate alone.

For the full configuration of an Edge Component behind an inspecting proxy, see [Running Edge Components behind TLS-inspecting proxies](../../user-guide/deploy-install/advanced-options/running-behind-secure-web-gateways.md).

*Example*:\
`/etc/aembit/cloud-ca.pem`, `C:\aembit\cloud-ca.pem`

***

### `CLIENT_WORKLOAD_ID`

Default - not set

OS-All

Associate Agent Proxy with a specific Client Workload by supplying that workload’s Aembit Client ID. Aembit uses this in conjunction with [Aembit Client Id](../../user-guide/access-policies/client-workloads/identification/aembit-client-id.md) configuration.

Set this to the value Aembit generates when you choose **Aembit Client ID** as the Client Workload’s Client Identification method. Despite this variable’s name, it doesn’t take the Client Workload’s own resource ID.

*Example*:\
`7e75e718-7634-480b-9f7b-a07bb5a4f11d`

***

### `HTTP_PROXY`

Default - not set

OS-All v1.31.4670

Specifies an upstream HTTP proxy for Agent Proxy outbound HTTP connections. When set, Agent Proxy routes outbound HTTP traffic through the specified proxy. Both `HTTP_PROXY` and `http_proxy` are honored on every platform.

*Example*:\
`http://proxy.example.com:8080`

***

### `HTTPS_PROXY`

Default - not set

OS-All v1.31.4670

Specifies an upstream HTTPS proxy for Agent Proxy outbound HTTPS connections. When set, Agent Proxy routes outbound HTTPS traffic through the specified proxy. Both `HTTPS_PROXY` and `https_proxy` are honored on every platform.

Agent Proxy supports only the `http` scheme for the proxy URL. For example, `HTTPS_PROXY=https://proxy.example.com:8080` is not valid.

*Example*:\
`http://proxy.example.com:8080`

***

### `K8S_NAMESPACE`

Default - not set

Kubernetes only

The Kubernetes namespace where Agent Proxy runs.

The Aembit Helm chart sets this automatically via the Kubernetes downward API (`fieldRef: metadata.namespace` in the chart’s webhook template).

When set, this variable is always available for use in [dynamic claims](../../user-guide/access-policies/credential-providers/advanced-options/dynamic-claims.md), regardless of whether it appears in `AEMBIT_ENV_VAR_ALLOWLIST`.

*Example*:\
`my-namespace`

***

### `K8S_POD_NAME`

Default - not set

Kubernetes only

The Kubernetes pod name where Agent Proxy runs.

The Aembit Helm chart sets this automatically via the Kubernetes downward API (`fieldRef: metadata.name` in the chart’s webhook template).

When set, this variable is always available for use in [dynamic claims](../../user-guide/access-policies/credential-providers/advanced-options/dynamic-claims.md), regardless of whether it appears in `AEMBIT_ENV_VAR_ALLOWLIST`.

*Example*:\
`my-app-pod-12345`

***

### `KRB5_CLIENT_KTNAME`

Default - not set

OS-Linux

The keytab Agent Proxy reads for its Kerberos principal. Takes precedence over `KRB5_KTNAME`. When you set neither, Agent Proxy reads `/etc/krb5.keytab`. Don’t set it together with `AEMBIT_PRIVILEGED_KEYTAB=true`.

*Example*:\
`/etc/aembit/agent-proxy.keytab`

***

### `KRB5_KTNAME`

Default - `/etc/krb5.keytab`

OS-Linux

The keytab Agent Proxy reads for its Kerberos principal when `KRB5_CLIENT_KTNAME` isn’t set. The `aembit_agent_proxy` Linux user must be able to read it; the default is root-only on most hosts. Don’t set it together with `AEMBIT_PRIVILEGED_KEYTAB=true`.

*Example*:\
`/etc/aembit/agent-proxy.keytab`

***

### `KUBERNETES_PROVIDER_ID`

Default - not set

Kubernetes only

A customer-defined identifier for the Kubernetes cluster where Agent Proxy runs. Used by integrations such as the [Wiz access conditions integration](../../user-guide/access-policies/access-conditions/integrations/wiz.md) and available for use in [dynamic claims](../../user-guide/access-policies/credential-providers/advanced-options/dynamic-claims.md), regardless of whether it appears in `AEMBIT_ENV_VAR_ALLOWLIST`.

Set this value through your Aembit Helm chart deployment by setting `agentProxy.env.KUBERNETES_PROVIDER_ID` to your cluster identifier.

*Example*:\
`arn:aws:eks:us-east-2:111122223333:cluster/my-cluster`

***

> **Installer doesn’t accept these variables**
>
> The Agent Proxy installer doesn’t accept `HTTP_PROXY`, `HTTPS_PROXY`, or `NO_PROXY`. To route Agent Proxy outbound traffic through an upstream HTTP proxy, set these variables in the Agent Proxy service’s environment so that the service inherits them:
>
> * **Windows:** Set them as [system environment variables](https://learn.microsoft.com/en-us/windows/win32/procthread/environment-variables). For required `NO_PROXY` entries on Windows Server, see [Install Agent Proxy on Windows Server](../../user-guide/deploy-install/virtual-machine/windows/agent-proxy-install-windows.md).
> * **Linux:** Set them in a [systemd drop-in file](https://www.freedesktop.org/software/systemd/man/latest/systemd.unit.html) using the [`Environment=` directive](https://www.freedesktop.org/software/systemd/man/latest/systemd.exec.html#Environment=).

### `NO_PROXY`

Default - not set

OS-All v1.31.4670

A comma-separated list of hosts or domains that should bypass the upstream proxy. Use this to exclude the Agent Controller, cloud metadata endpoints, and other local services from proxy routing. Both `NO_PROXY` and `no_proxy` are honored on every platform.

Include the following entries so that Agent Proxy reaches the Instance Metadata Service (IMDS) endpoints it uses to attest workloads on AWS, Azure, and GCP.

| Entry                      | Purpose                                                           |
| -------------------------- | ----------------------------------------------------------------- |
| `169.254.169.254`          | AWS, Azure, and GCP instance metadata                             |
| `169.254.170.2`            | AWS Elastic Container Service (ECS) task metadata and credentials |
| `metadata.google.internal` | GCP instance metadata                                             |

You may need to include additional hosts, depending on your deployment.

**Supported patterns:**

Aembit guarantees the following patterns match:

* Specific IP addresses (for example, `169.254.169.254`)
* Specific hostnames (for example, `metadata.google.internal`)

Other patterns may cause unexpected behavior. For more information or help, contact [Aembit Support](../../support-overview.md).

*Example*:\
`aembit.io,169.254.169.254,metadata.google.internal`

## Agent Injector environment variables

### `AEMBIT_LOG_LEVEL` (Replaces `AEMBIT_LOG`)

Default - `info`

OS-All

Set the Agent Injector log level. The supported levels include `error`, `warn`, `info` (default value), `debug`, `trace`, and `off`. See [Log level reference](agent-log-level-reference.md) for details.

*Example*:\
`warn`

## Aembit CLI environment variables

Here is a list of all available environment variables for configuring the [Aembit CLI](../../dev-guide/cli/overview.md):

> **Environment variable and command option priority**
>
> You can configure the Aembit CLI using both environment variables and command options. Command options take precedence, overriding any corresponding environment variables.
>
> For example, if you’ve set a value with the `--client-id` option, Aembit CLI uses that over the `AEMBIT_CLIENT_ID` environment variable. This lets you establish a default configuration with environment variables and override specific settings for individual commands as needed.

### `AEMBIT_CLIENT_ID` Required

Default - not set

OS-All

This value represents the Edge SDK Client ID from your Aembit Trust Provider. Aembit automatically generates the Edge SDK Client ID when you configure a Trust Provider in your Aembit Tenant UI. To retrieve your Edge SDK Client ID, see [Find your Edge SDK Client ID](../../user-guide/access-policies/trust-providers/get-edge-sdk-client-id.md).

*Example*:\
`aembit:useast2:a12bc3:identity:github_idtoken:63ab7be6-9785-4a14-be1c-2acf0253070b`

***

### `AEMBIT_CLIENT_TLS_PRIVATE_KEY`

Default - not set

OS-All v1.32

The path to a PEM-encoded PKCS #8 private key file used to retrieve an X.509-SVID certificate. The certificate comes from an [X.509-SVID Credential Provider](../../user-guide/access-policies/credential-providers/about-spiffe-x509-svid.md). Aembit CLI generates a Certificate Signing Request (CSR) from the supplied key, submits it through the credential retrieval flow, and returns the signed certificate chain in `CLIENT_CERT_CHAIN`. The private key never leaves the local machine.

Equivalent to the [`--client-tls-private-key`](../../dev-guide/cli/reference/credentials-get.md#--client-tls-private-key) flag on `aembit credentials get`.

*Example*:\
`/etc/aembit/client.key`

***

### `AEMBIT_ENV_VAR_ALLOWLIST`

Default - not set (empty)

OS-All v1.31.4764

A comma-separated list of environment variable names that Aembit CLI may capture for use in [dynamic claims](../../user-guide/access-policies/credential-providers/advanced-options/dynamic-claims.md). By default, the allowlist is empty, so Aembit CLI captures no custom environment variables.

The CLI inherits the shell environment automatically; you only need to set the allowlist itself explicitly.

*Example*:\
`CORPORATE_APP_ID,WEBSITE_HOSTNAME`

***

### `AEMBIT_LOG_LEVEL`

Default - `warn`

OS-All

The log level to use for the Aembit CLI. This controls the verbosity of the output from the CLI. The supported levels include `off`, `trace`, `debug`, `info`, `warn`, `error`.

*Example*:\
`debug`

***

### `AEMBIT_RESOURCE_SET_ID`

Default - not set

OS-All

The [Resource Set](../../user-guide/administration/resource-sets/overview.md) to authenticate against and within which the Access Policy matching happens. This is useful for when you want to use a specific Resource Set for your credentials. You can find the Resource Set ID in your Aembit Tenant UI under the Resource Sets section.

*Example*:\
`78bg7be6-9301-hj14-d51c-2acf02530y67`

***

### `CLIENT_WORKLOAD_ID`

Default - not set

OS-All

Selects a specific Client Workload by supplying that workload’s Aembit Client ID. Set this when two or more Client Workloads attest through the same Trust Provider and would otherwise match the same Access Policy.

Equivalent to the [`--client-workload-id`](../../dev-guide/cli/reference/credentials-get.md#--client-workload-id) flag on `aembit credentials get`.

Despite this variable’s name, it doesn’t take the Client Workload’s own resource ID. Unlike the other Aembit CLI variables, this name has no `AEMBIT_` prefix.

*Example*:\
`7e75e718-7634-480b-9f7b-a07bb5a4f11d`
