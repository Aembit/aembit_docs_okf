---
type: explanation
title: "About Aembit Edge"
description: "Understanding Aembit Edge and its role as the distributed enforcement layer within your environments"
resource: https://docs.aembit.io/get-started/concepts/aembit-edge/
tags: ["concept"]
timestamp: 2026-09-09T15:44:57-07:00
---

# About Aembit Edge

Aembit Edge is the collection of components deployed directly within your operational environments. These components enforce Access Policies and enable secretless workload communication. It functions as a distributed enforcement and interaction layer, positioned within your compute environments alongside your workloads—spanning Kubernetes clusters, virtual machines, and serverless platforms.

The Edge architecture separates the control plane (Aembit Cloud) from the data plane (where workload traffic flows).

Aembit Cloud makes authorization decisions and manages credential lifecycles. Aembit Edge components handle traffic interception, credential injection, and forwarding locally within your environment. This design ensures that your sensitive workload data remains within your network boundaries and never passes through Aembit’s infrastructure.

Aembit Edge is essential for translating centralized policies into concrete access control actions at the point where your workloads interact. It eliminates the need for applications to store or manage long-lived secrets by intercepting requests, verifying identities, and injecting short-lived credentials just-in-time.

![](https://docs.aembit.io/aembit-icons/gears-light.svg)

[Start deploying Aembit Edge](../../user-guide/deploy-install/overview.md)See Aembit Edge deployment in the User Guide

→

### Edge Component registration

Before Aembit Edge can enforce access control, first you must deploy it within your operational environments. This involves installing the necessary components that intercept workload traffic, gather identity evidence, and inject credentials as needed.

Upon deployment, Aembit Edge components must register with Aembit Cloud to establish trust and enable policy synchronization. This registration process typically involves the following steps:

1. **Controller Registration** - Agent Controller registers with Aembit Cloud to establish trust. Agent Controller has two registration options: using a Device Code flow or by providing a Controller ID and configured Trust Providers.

2. **Proxy Retrieves Token** - Agent Proxy registers with Agent Controller to obtain a token for authenticating with Aembit Cloud. This is typically done via an HTTP/S call to the Agent Controller API endpoint `/api/token`.

3. **Aembit Cloud Grants Token** - Aembit Cloud verifies the request and grants Agent Proxy a token. The Agent Proxy uses this token to authenticate with Aembit Cloud.

4. **Proxy Registration with Aembit Cloud** - The Agent Proxy uses the obtained token to register with Aembit Cloud, allowing it to receive Access Policies and interact with the Aembit Cloud services.

   From there, Agent Proxy can start intercepting outbound requests from Client Workloads, gathering identity evidence, and [injecting credentials](#credential-injection) as needed based on the Access Policies defined in Aembit Cloud.

![Aembit Edge registration process showing Agent Controller and Agent Proxy registering with Aembit Cloud](https://docs.aembit.io/d2/docs/get-started/concepts/aembit-edge-0.svg)

## Credential injection

Once Aembit Edge registers with Aembit Cloud and is operational, it can perform **credential injection** to enable secure workload communication. This process allows Client Workloads to access Server Workloads without needing to store or manage long-lived credentials. Aembit Edge intercepts outbound requests from Client Workloads, gathers identity evidence, and injects short-lived credentials just-in-time based on the evaluated Access Policy.

The credential injection process typically follows these steps:

1. **Request Interception** - Agent Proxy intercepts outbound requests from the Client Workload. This interception allows Aembit to gather identity evidence and contextual information about the Client Workload and its runtime environment.

2. **Identity Attestation** - Agent Proxy collects identity attributes and contextual information about the Client Workload, such as Kubernetes service account tokens, cloud provider metadata, or process information.

3. **Credential Request** - Agent Proxy directly requests the necessary short-lived access credentials from Aembit Cloud for the target Server Workload based on the evaluated Access Policy.

4. **Credential Retrieval** - Aembit Cloud interacts with the configured Credential Provider to obtain the necessary short-lived access credentials and returns them to the Agent Proxy.

5. **Credential Injection** - Agent Proxy receives the credentials and injects them just-in-time into the original client request, modifying headers, connection parameters, or authentication fields as required.

6. **Request Forwarding** - Agent Proxy forwards the modified request to the target Server Workload, which can now authenticate the Client Workload using the injected credentials.

The following diagram illustrates this process:

![Aembit Edge credential injection flow from Client Workload through Agent Proxy to Server Workload](https://docs.aembit.io/d2/docs/get-started/concepts/aembit-edge-1.svg)

## Network protocols

Aembit Edge components communicate via the HTTP application protocol. The specific version used depends on where the traffic flows:

* **Communication with Aembit Cloud uses gRPC with HTTP/2.** Agent Proxy and Agent Controller send operational traffic—such as policy synchronization, credential requests, logging, and audit data—to Aembit Cloud over HTTP/2. The initial authentication handshake is the exception and uses HTTP/1.1.
* **Traffic that stays within your environment uses HTTP/1.1.** This includes workload traffic routed through Agent Proxy and the connections between Edge components.

The following diagram shows which protocol each connection uses:

![HTTP protocol used for each Aembit Edge connection](https://docs.aembit.io/d2/docs/get-started/concepts/aembit-edge-2.svg)

The following table summarizes the protocol used for each connection:

| Connection                                        | Protocol                              |
| ------------------------------------------------- | ------------------------------------- |
| Workload traffic routed through Agent Proxy       | HTTP/1.1                              |
| Agent Proxy to Agent Controller (token retrieval) | HTTP/1.1                              |
| Agent Proxy to Network Identity Attestor          | HTTP/1.1                              |
| Agent Proxy to Aembit Cloud                       | HTTP/2 (authentication uses HTTP/1.1) |
| Agent Controller to Aembit Cloud                  | HTTP/2 (authentication uses HTTP/1.1) |
| Component health endpoints                        | HTTP/1.1                              |

Agent Proxy handles proxied workload traffic over HTTP/1.1. If your HTTP client or automation tool negotiates HTTP/2 by default, configure it to use HTTP/1.1 when routing requests through Agent Proxy.

## Supported deployment environments

Aembit designed Edge components for deployment across diverse modern computing environments:

**Container Orchestration**

* [Kubernetes deployment](../../user-guide/deploy-install/kubernetes/overview.md) - Agent Controller and Agent Injector deployed via Helm chart, with Agent Proxy automatically injected as a sidecar container
* [Amazon ECS deployment](../../user-guide/deploy-install/serverless/aws-ecs-fargate.md) - Components deployed as ECS tasks and services using Terraform modules

**Virtual Machines**

Each installation guide lists supported versions:

* [Linux deployment](../../user-guide/deploy-install/virtual-machine/linux/overview.md) - Downloadable installers for Ubuntu LTS and Red Hat Enterprise Linux with SELinux support
* [Windows deployment](../../user-guide/deploy-install/virtual-machine/windows/overview.md) - MSI packages for Windows Server environments

**CI/CD Platforms**

* [GitHub Actions](../../user-guide/deploy-install/ci-cd/github/overview.md) - Agent Proxy deployed as a GitHub Action for workflow-based access control
* [GitLab CI/CD](../../user-guide/deploy-install/ci-cd/gitlab/overview.md) - Agent Proxy deployed as a GitLab Runner for pipeline-based access control
* [Jenkins Pipelines](../../user-guide/deploy-install/ci-cd/jenkins-pipelines.md) - Agent Proxy deployed as a Jenkins Pipeline step for job-based access control

**Serverless Platforms**

* [AWS Lambda containers](../../user-guide/deploy-install/serverless/overview.md) - Agent Proxy deployed as a Lambda Extension layer for containerized functions
* [AWS Lambda functions](../../user-guide/deploy-install/overview.md) - Agent Proxy deployed as a Lambda layer for standard Lambda functions

**Specialized Deployments**

* [High availability configurations](../../user-guide/deploy-install/advanced-options/agent-controller/agent-controller-high-availability.md) - Multiple Agent Controller instances with load balancing

## Benefits of using Aembit Edge

* **Local Traffic Control** - Intercepts and processes workload traffic within your environment, ensuring sensitive data never leaves your network boundaries while Aembit enforces Access Policies.
* **Secretless Architecture** - Eliminates the need for workloads to store or manage long-lived credentials by handling credential injection transparently at the network layer.
* **Environment Integration** - Deploys natively within your existing infrastructure using standard tools like Helm, installers, and container images without requiring application code changes.
* **Distributed Enforcement** - Provides consistent policy enforcement across heterogeneous environments while maintaining centralized policy management through Aembit Cloud.
* **Performance Optimization** - Processes requests locally to minimize latency and includes credential caching to maintain availability during temporary network disruptions.
