---
type: how-to
title: "Edge Component container image best practices"
description: "Best practices for deploying official Aembit container images"
resource: https://docs.aembit.io/user-guide/deploy-install/container-image-best-practices/
interface: web-ui
tags: [deploy-install]
timestamp: 2025-06-24T08:43:36-07:00
type_inferred: true
---

# Edge Component container image best practices

Aembit built its official [Aembit container images](https://hub.docker.com/u/aembit) to streamline the deployment process. Aembit provides a [Helm chart for Kubernetes](kubernetes/kubernetes.md) and a [Terraform module for ECS](serverless/aws-ecs-fargate.md) that ease deployment in containerized environments.

If these are incompatible with your deployment environment, you may run into issues as you hand craft a Kubernetes configuration or an ECS task definition.

The details on this page help you as you follow your own path.

## Container user IDs

[Section titled “Container user IDs”](#container-user-ids)

Some container images declare a specific user ID that the containerized application expects to run as. The following table lists Aembit container images and their expected user IDs:

| Container Image       | User ID |
| --------------------- | ------- |
| `aembit_agent_proxy`  | `65534` |
| `aembit_sidecar_init` | `26248` |

You shouldn’t need to specify these user IDs unless you define a pod-level `securityContext/runAsUser` attribute in a Kubernetes deployment or extend the container image in a way that changes the default user ID. If you’ve specified the wrong user for either the `aembit-agent-proxy` container or the `aembit-init-iptable` container, you’ll see a log error message such as:

```shell
sudo: you do not exist in the passwd database
```

Since the v1.22 release of the Aembit Helm chart, the injected container definitions include `securityContext/runAsUser` attributes that override any such pod-level attribute. Since the v1.22 release of the Aembit Agent Injector, you’ll see a warning message:

```shell
The injected container (...) is unlikely to run correctly because it will run as UID ... where UID ... is expected."
```

If you see this warning, you must make sure to specify the `securityContext/runAsUser` attribute for each of the Aembit containers that you are injecting into any Client Workload pods that specify a `securityContext/runAsUser` attribute at the pod-level.

## Client Workload user IDs

[Section titled “Client Workload user IDs”](#client-workload-user-ids)

Transparent Steering relies on the user ID of the process initiating a network connection to exempt the Agent Proxy outbound connections. Therefore any Client Workload that runs under the `65534` UID (commonly named `nobody`) is also exempt from Transparent Steering.

## Write-accessible filesystem

[Section titled “Write-accessible filesystem”](#write-accessible-filesystem)

The `aembit_agent_proxy` container image depends on being able to write to the root filesystem to download your tenant’s CA certificate and add it to the trusted certificate bundle. If you turn off writing to the root filesystem, Agent Proxy logs an error message similar to the following:

```shell
Error when fetching token. Will attempt to refresh in 16 seconds. Error: error sending request ... invalid peer certificate: UnknownIssuer
```

ECS and Kubernetes use slightly different spelling, using a different letter casing, for the same setting:

* `readonlyRootFilesystem` on ECS
* `readOnlyRootFilesystem` on Kubernetes

## Verify container image signatures

[Section titled “Verify container image signatures”](#verify-container-image-signatures)

Aembit cryptographically signs all [container images in Docker Hub](https://hub.docker.com/u/aembit) so you can verify the authenticity of them before deploying them in your environments. See [Verifying container image signatures](verify-container-images.md) for more details.
