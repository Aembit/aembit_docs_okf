---
type: how-to
title: "Agent Proxy termination strategy"
description: "Learn about Agent Proxy's termination strategies across different environments and how to configure the AEMBIT_SIGTERM_STRATEGY variable"
resource: https://docs.aembit.io/user-guide/deploy-install/advanced-options/agent-proxy/agent-proxy-termination-strategy/
interface: web-ui
tags: [agent-proxy, advanced-option, deploy-install]
timestamp: 2025-05-22T22:52:17-07:00
type_inferred: true
---

# Agent Proxy termination strategy

Agent Proxy must be able to serve Client Workload traffic throughout the entire lifecycle of the Client Workload. When both the Client Workload and Agent Proxy receive a termination signal (`SIGTERM`), the Agent Proxy attempts to continue operating and serving traffic until the Client Workload exits.

Agent Proxy runs in distinct environments, such as Virtual Machines, Kubernetes, and ECS Fargate, where workload lifecycles can differ. To handle these variations, Agent Proxy uses different termination strategies.

## Configuration

[Section titled “Configuration”](#configuration)

You can configure the termination strategy by setting the `AEMBIT_SIGTERM_STRATEGY` environment variable.

The supported values are:

* `immediate` – Exits immediately upon receiving `SIGTERM`.
* `sigkill` – Ignores the `SIGTERM` signal and waits for a `SIGKILL`.

## Default termination strategies

[Section titled “Default termination strategies”](#default-termination-strategies)

The following table lists the default termination strategy for each environment. You can override the default behavior using the `AEMBIT_SIGTERM_STRATEGY` environment variable.

| Environment               | Default Termination Strategy |
| ------------------------- | ---------------------------- |
| AWS ECS Fargate           | `sigkill`                    |
| AWS Lambda function       | `immediate`                  |
| AWS Lambda container      | `immediate`                  |
| Docker-compose on VMs     | `sigkill`                    |
| Kubernetes                | `sigkill`                    |
| Virtual Machine (Linux)   | `immediate`                  |
| Virtual Machine (Windows) | N/A                          |
| Virtual Appliance         | `immediate`                  |
