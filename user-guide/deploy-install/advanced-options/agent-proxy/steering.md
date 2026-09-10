---
type: explanation
title: "About traffic steering methods"
description: "How different traffic steering methods and how to configure them for different deployment models"
resource: https://docs.aembit.io/user-guide/deploy-install/advanced-options/agent-proxy/steering/
tags: ["agent-proxy", "advanced-option", "deploy-install"]
timestamp: 2026-09-09T15:44:57-07:00
---

# About traffic steering methods

Traffic steering is the process of directing network traffic from Client Workloads to an Agent Proxy, which inspects and modifies this traffic.

Selecting the appropriate steering method depends on factors such as the deployment model, protocol compatibility, and the level of control required over traffic management.

Certain deployment models offer flexibility, allowing you to select the steering method that best suits your needs. In other cases, the deployment model dictates the steering method.

## Conceptual overview

Traffic steering methods determine how network traffic from Client Workloads reaches the Agent Proxy. Three primary methods exist:

* **Transparent Steering** - Automatically redirects all TCP traffic without client configuration.
* **Selective Transparent Steering** - Automatically redirects TCP traffic only for specified hostnames without client configuration.
* **Explicit Steering** - Requires explicit client-side configuration to route traffic.

## Method comparison and protocol support

| Deployment Model                        | Explicit Steering | Transparent Steering | Selective Transparent Steering |
| --------------------------------------- | ----------------- | -------------------- | ------------------------------ |
| Kubernetes (K8S)                        | ✅                 | ✅ (default)          | ❌                              |
| Kubernetes on Fargate                   | ✅ (default)       | ❌                    | ❌                              |
| OpenShift                               | ✅ (default)       | ❌                    | ❌                              |
| Virtual Machines (VM) - Linux           | ✅                 | ✅ (default)          | ✅                              |
| Virtual Machines (VM) - Windows         | ✅ (default)       | ❌                    | ❌                              |
| Elastic Container Service (ECS) Fargate | ✅ (default)       | ❌                    | ❌                              |
| AWS Lambda Extension                    | ✅ (default)       | ❌                    | ❌                              |

**Protocol Support** -

* **Transparent Steering** - All supported protocols.
* **Selective Transparent Steering** - All supported protocols.
* **Explicit Steering** - HTTP-based protocols only.

## Technical details and configuration

### Transparent steering

Transparent Steering automatically redirects all TCP traffic using `iptables` without requiring any client-side awareness. It’s straightforward, minimizing configuration overhead. Transparent Steering is the default method for Kubernetes (K8S) and Virtual Machine (VM) deployments and doesn’t require additional configuration.

### Selective transparent steering

Selective Transparent Steering redirects TCP traffic only for specified hostnames, providing precise control without explicit client configuration.

* Turned off by default.
* Available exclusively for virtual machines.
* Enable this by setting the environment variable `AEMBIT_STEERING_ALLOWED_HOSTS` during installation:

```shell
AEMBIT_STEERING_ALLOWED_HOSTS=graph.microsoft.com,vault.mydomain [...] ./install
```

For further information, see the [Agent Proxy Virtual Machine Installation Guide](../../virtual-machine/linux/agent-proxy-install-linux.md).

### Explicit steering

Explicit steering directs Client Workloads traffic based on specific configurations. It’s the default steering method for the Elastic Container Service (ECS) Fargate and AWS Lambda Extension deployment models. Explicit Steering is also an optional configuration for Kubernetes deployments.

In Kubernetes, enable explicit steering by setting the `aembit.io/steering-mode` annotation on a Client Workload:

```yaml
aembit.io/steering-mode: explicit
```

For Kubernetes-specific installation details and annotation configurations, refer to the [Kubernetes Installation Guide](../../kubernetes/kubernetes.md).

#### Explicit steering port configuration

Agent Proxy listens on port `8000` for traffic sent using explicit steering. If this conflicts with an existing application port, override it using the `AEMBIT_HTTP_SERVER_PORT` environment variable.

#### Explicit steering examples

Many ways exist to configure Client Workloads to use explicit steering. Common methods include setting environment variables such as `HTTP_PROXY` or `HTTPS_PROXY`. However, specific applications might provide their own explicit configuration methods to route traffic via a proxy.

The following are examples:

* **Go applications** -
  * Using the `HTTPS_PROXY` environment variable, widely recognized by many HTTP libraries:

```shell
export HTTPS_PROXY=localhost:8000
./run_go_app [...]
```

* **Using `curl` command** -
  * Explicitly specifying proxy configuration via a command-line argument:

```shell
curl -x localhost:8000 myserverworkload
```

* **HashiCorp Vault CLI** -
  * Configuring the HashiCorp Vault-specific environment variable to route traffic via the proxy:

```shell
export VAULT_HTTP_PROXY="http://localhost:8000"
vault token lookup
```

* **Java/JVM-based applications** -
  * Configuring the JVM-specific defined values to route traffic via the proxy:

```java
java ...
  -Dhttp.proxyHost=localhost
  -Dhttp.proxyPort=8000
  -Dhttps.proxyHost=localhost
  -Dhttps.proxyPort=8000
  -Dhttp.nonProxyHosts=*.cluster.local|*.svc
  ...
```
