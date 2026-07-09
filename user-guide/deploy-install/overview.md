---
type: explanation
title: "Install and Deploy Aembit Edge"
description: "This document provides a high-level conceptual overview of how Aembit Edge handles Workload connections"
resource: https://docs.aembit.io/user-guide/deploy-install/
tags: [deploy-install]
timestamp: 2026-04-13T13:05:24-07:00
type_inferred: true
---

# Install and Deploy Aembit Edge

Aembit manages the identities of and access from workloads (typically, software applications) to services.

Aembit provides Aembit Edge, software components deployed in your environment that intermediate connections between workloads, gather assessment data from your operating environment, inject credentials into requests, and log interactions between Client Workloads and services.

For each deployment type, this page describes the multiple connections and protocols used to enable Aembit in support of your workloads.

## Aembit Edge - data plane

[Section titled “Aembit Edge - data plane”](#aembit-edge---data-plane)

Aembit Edge Components include:

* Aembit Agent Proxy
* Aembit Agent Controller
* Aembit Agent Injector (Kubernetes Only)
* Aembit Agent Sidecar Init (Kubernetes Only)

Before diving into these components, it’s important to understand the fundamentals of workload communication and Aembit’s role in the process. At its most basic level, a Client Workload communicates with a Server Workload using a transport protocol, such as TCP, utilizing a set of IP addresses and ports to exchange data. Aembit is generally based on a Proxy model and will intercept the network communication between Client and Server Workloads, authenticating the connection as configured by an Aembit Access Policy.

## Deployment

[Section titled “Deployment”](#deployment)

To achieve these capabilities, the Aembit Architecture depends on deploying Agent Controller instances, which Agent Proxy instances can then leverage to bootstrap secure communication with the Aembit Cloud.

From a network/protocol perspective, that deployment is achieved by the following steps:

1. Deploy Agent Controller with Device Code or Agent Controller ID.

   * Device Code: Authenticates and registers with the Aembit Cloud using the time-bound and single-use Device Code created for a specific Agent Controller.
   * Agent Controller ID: Authenticates and registers with the Aembit Cloud using the TrustProvider with the associated Agent Controller.

2. Deploy Agent Proxy configured to communicate with an Agent Controller.

   * Agent Proxy registers with the Agent Controller and Aembit Cloud.
   * Optional: You can configure Agent Controller with a TLS Certificate to enable and enforce HTTPS communication.

### Virtual machine

[Section titled “Virtual machine”](#virtual-machine)

![Aembit Edge authentication and registration for VM deployments](https://docs.aembit.io/d2/docs/user-guide/deploy-install/index-0.svg)

### Kubernetes

[Section titled “Kubernetes”](#kubernetes)

![Aembit Edge authentication and registration for Kubernetes deployments](https://docs.aembit.io/d2/docs/user-guide/deploy-install/index-1.svg)

### AWS ECS Fargate

[Section titled “AWS ECS Fargate”](#aws-ecs-fargate)

![Aembit Edge authentication and registration for AWS ECS Fargate deployments](https://docs.aembit.io/d2/docs/user-guide/deploy-install/index-2.svg)

## Workload communication

[Section titled “Workload communication”](#workload-communication)

After the Aembit Edge is deployed and registered, we can now begin identifying workloads and managing access for the configured policies.

1. Client Workloads connect to Server Workloads - the Agent Proxy handles both DNS and application traffic.

   1. **DNS** - DNS requests and responses are read to route application traffic.
   2. **Application Traffic** - Uses the configured Access Policy and Credentials from the Aembit Cloud for authorized injection.

![Aembit Edge application traffic flow showing DNS and app traffic routing](https://docs.aembit.io/d2/docs/user-guide/deploy-install/index-3.svg)
