---
type: troubleshooting
title: "Agent Controller Health"
description: "This page describes steps for troubleshooting issues with Agent Controller health."
resource: https://docs.aembit.io/user-guide/troubleshooting/agent-controller-health/
interface: web-ui
tags: ["troubleshooting"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Agent Controller Health

### Potential culprit

The Agent Controller is a critical Aembit Edge Component that facilitates Agent Proxy registration. For any production deployment, it’s essential to install and configure the [Agent Controller in a high availability configuration](../deploy-install/advanced-options/agent-controller/agent-controller-high-availability.md) and enable health monitoring.

It is common to skip the high availability configuration and monitoring for proof-of-concept deployments. This oversight may lead to issues if the Agent Controller enters an unhealthy state.

Several common causes can lead to this situation:

* The Agent Controller was configured to use Trust Provider-based registration, and the Trust Provider was misconfigured (either originally or mistakenly altered afterward).
* The Agent Controller was configured to use a device code, and an expired or incorrect device code was used.

In both scenarios, the Agent Controller will be unable to register, leading to the Agent Proxy’s inability to register and retrieve credentials from the Aembit cloud.

### Troubleshooting Steps

#### Agent Controller Deployed on Virtual Machine

To check the health of the Agent Controller, query the [Agent Controller Health endpoint](../deploy-install/advanced-options/agent-controller/agent-controller-high-availability.md#agent-controller-health-endpoint-swagger-documentation).

Execute the following command to assess the health of the Agent Controller:

```shell
curl http://<AgentControllerHost>:5000/health
```

#### Agent Controller Deployed on Kubernetes

Execute the following command to assess the health of the Agent Controller:

```shell
kubectl get pods -n aembit -l aembit.io/component=agent-controller
```

#### Resolving issues

If the Agent Controller is not healthy:

* Check the Trust Provider configuration if it was deployed via Trust Provider-based registration.
* If the Agent Controller was deployed with device code registration, generate a new device code and redeploy the Agent Controller.
