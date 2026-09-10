---
type: how-to
title: "Kubernetes Pod Name"
description: "This document describes how the Kubernetes Pod Name Prefix method identifies Client Workloads in Aembit."
resource: https://docs.aembit.io/user-guide/access-policies/client-workloads/identification/kubernetes-pod-name/
interface: web-ui
tags: ["kubernetes", "identification", "client-workload", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Kubernetes Pod Name


In Kubernetes environments, each pod is assigned a unique name within its namespace. The Kubernetes Pod Name identification method allows you to target a specific individual pod by specifying its exact name. This is particularly useful for managing access for standalone pods that are not part of a deployment or for pods with unique names that need to be individually managed.

## Applicable Deployment Type

This method is suitable for Edge-based deployments.

## Configuration

### Aembit Cloud

1. Create a new Client Workload.
2. Choose **Kubernetes Pod Name** for client identification.
3. In the **Value** field, enter the desired pod name.

#### Finding the Pod Name:

* Use the `kubectl get pods` command to list all pods in your cluster.
* Identify the specific pod you want to target and note its exact name.
* Use this exact name as the **Value** in the Client Workload configuration.

## Related

**Compatible trust providers**

* [Kubernetes Service Account](../../trust-providers/kubernetes-service-account-trust-provider.md)
