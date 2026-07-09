---
type: how-to
title: "Selective Transparent Steering"
description: "This page describes the selective transparent steering feature."
resource: https://docs.aembit.io/user-guide/deploy-install/advanced-options/agent-proxy/selective-transparent-steering/
interface: web-ui
tags: [agent-proxy, advanced-option, deploy-install]
timestamp: 2026-06-05T14:13:51-07:00
type_inferred: true
---

# Selective Transparent Steering

Selective transparent steering lets you control egress traffic by specifying which destinations Agent Proxy handles.

By default, Agent Proxy proxies all egress traffic from a host where you install it. Selective transparent steering restricts this proxied traffic to a specific list of hostnames. When you enable this feature, Agent Proxy only proxies egress traffic to the hostnames you specify. This gives you more precise control over which destinations Agent Proxy manages.

### Usage

[Section titled “Usage”](#usage)

Selective transparent steering is **off** by default. To enable this feature, add the environment variable `AEMBIT_STEERING_ALLOWED_HOSTS` when installing Agent Proxy. Set the variable’s value to a comma-separated list of hostnames that Agent Proxy should proxy.

Note

This setting applies to VM deployments only. Kubernetes, AWS Lambda, and ECS Fargate deployments don’t support it.

```shell
AEMBIT_STEERING_ALLOWED_HOSTS=graph.microsoft.com,vault.mydomain [...] ./install
```
