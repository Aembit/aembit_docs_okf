---
type: explanation
title: "Selective Transparent Steering"
description: "This page describes the selective transparent steering feature."
resource: https://docs.aembit.io/user-guide/deploy-install/advanced-options/agent-proxy/selective-transparent-steering/
tags: ["agent-proxy", "advanced-option", "deploy-install"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Selective Transparent Steering

Selective transparent steering lets you control egress traffic by specifying which destinations Agent Proxy handles.

By default, Agent Proxy proxies all egress traffic from a host where you install it. Selective transparent steering restricts this proxied traffic to a specific list of hostnames. When you enable this feature, Agent Proxy only proxies egress traffic to the hostnames you specify. This gives you more precise control over which destinations Agent Proxy manages.

### Usage

Selective transparent steering is **off** by default. To enable this feature, add the environment variable `AEMBIT_STEERING_ALLOWED_HOSTS` when installing Agent Proxy. Set the variable’s value to a comma-separated list of hostnames that Agent Proxy should proxy.

> **Note**
>
> This setting applies to VM deployments only. Kubernetes, AWS Lambda, and ECS Fargate deployments don’t support it.

```shell
AEMBIT_STEERING_ALLOWED_HOSTS=graph.microsoft.com,vault.mydomain [...] ./install
```
