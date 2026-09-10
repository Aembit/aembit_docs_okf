---
type: how-to
title: "How to configure explicit steering"
description: "How to use the Explicit Steering feature to direct specific traffic to the Agent Proxy"
resource: https://docs.aembit.io/user-guide/deploy-install/advanced-options/agent-proxy/explicit-steering/
interface: web-ui
tags: ["agent-proxy", "advanced-option", "deploy-install"]
timestamp: 2026-09-08T23:32:41-07:00
---

# How to configure explicit steering

The Explicit Steering feature enables you to route and direct specific traffic in a Kubernetes deployment to the Agent Proxy.

> **Note**
>
> By default, in Kubernetes deployment, all traffic in a given pod is sent to the Agent Proxy.

## Configure Explicit Steering

To configure explicit steering in your Kubernetes cluster, simply follow the steps described on the [Kubernetes Deployment](../../kubernetes/kubernetes.md) page in the Aembit Technical Documentation and set the `aembit.io/steering-mode` annotation to `explicit`. This sets the steering mode to `explicit`.

Once you have set the steering mode to `explicit`, each Client Workload that wants to use Aembit will need to be configured to use Agent Proxy as its HTTP proxy.

The default port used for explicit steering is `8000`. In the case, it conflicts with a port that the Client Workload uses. The explicit port number may be overridden via the `AEMBIT_HTTP_SERVER_PORT` environment variable.

The following section provides several examples of how Agent Proxy is used as an HTTP proxy.

## Examples

The section below shows several different Client Workload examples using different applications with Agent Proxy as an HTTP proxy.

### Example Client Workload using `curl` with `-x` to specify an HTTP proxy

```sh
curl -x localhost:8000 myserverworkload
```

### Example Client Workload using HashiCorp Vault CLI (Vault CLI implicitly uses VAULT\_HTTP\_PROXY)

```shell
export VAULT_HTTP_PROXY="http://localhost:8000"
vault token lookup
```

### Example Client Workload written in Go (Go’s HTTP client implicitly uses HTTPS\_PROXY)

```shell
export HTTPS_PROXY=localhost:8000
./run_go_app [...]
```

### Example Client Workload written in Java applications

```java
java ...
  -Dhttp.proxyHost=localhost
  -Dhttp.proxyPort=8000
  -Dhttps.proxyHost=localhost
  -Dhttps.proxyPort=8000
  -Dhttp.nonProxyHosts=*.cluster.local|*.svc
  ...
```

> **Caution**
>
> Java, unlike other programming languages (Python, Node.js) doesn’t respect proxy configurations via environment variables set at the OS-level. Java applications require the `proxyHost` and `proxyPort` properties as [documented](https://docs.oracle.com/javase/6/docs/technotes/guides/net/proxies.html).
