---
type: reference
title: "How to shutdown Agent Proxy using HTTP"
description: "How to shut down the Agent Proxy using HTTP"
resource: https://docs.aembit.io/user-guide/deploy-install/advanced-options/agent-proxy/agent-proxy-shutdown/
interface: web-ui
tags: ["agent-proxy", "advanced-option", "deploy-install"]
timestamp: 2026-09-08T23:32:41-07:00
---

# How to shutdown Agent Proxy using HTTP

## Introduction

In certain scenarios, it may be necessary to manually shut down the Agent Proxy when the main container has exited but the sidecar process continues running.

This situation commonly occurs with Kubernetes jobs, where the main container exits upon job completion. Terminating the entire job in this case might appear as a cancelled job. To avoid that, Aembit provides a way to gracefully stop the sidecar, allowing the job to complete cleanly.

## Agent Proxy Shutdown

The Agent Proxy can be shut down by sending an HTTP `POST` request to its `/quit` endpoint.

### Example Command

An example command using `curl`:

```shell
curl -X POST localhost:<HEALTH_CHECK_PORT>/quit
```

When the Agent Proxy is properly configured to receive this request, it will flush any remaining events to the backend before exiting gracefully.

## Configuration Flags

The behavior of the Agent Proxy can be controlled through specific environment variables outlined below:

`AEMBIT_ENABLE_HTTP_SHUTDOWN` Environment Variable

This variable controls whether the Agent Proxy supports the `/quit` endpoint.

* **Default Value** - `false`
* **Accepted Values** - `false` or `true`

`AEMBIT_SERVICE_PORT` Environment Variable

This variable specifies the port on which the Agent Proxy responds to the diagnostic and configuration endpoint `/quit`.

* **Default Value** - `51234`
* **Accepted Values** - an integer number in the range 1 to 65535 (inclusive)

### Accessibility and Security Considerations

> **Note**
>
> Handler endpoints, including `/quit`, are only accessible via `localhost` or `127.0.0.1`. This setting is non-configurable to ensure security.

> **Caution**
>
> The `/quit` handler should only be enabled in fully trusted environments. When enabled, any application with network access to `127.0.0.1` can send a request to shut down the Agent Proxy.

## Recommended Environments

> **Note**
>
> The `/quit` handler is intended for use primarily within **Kubernetes** environments.
