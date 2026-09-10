---
type: how-to
title: "Kubernetes Pod Name Prefix"
description: "This document describes how the Kubernetes Pod Name Prefix method identifies Client Workloads in Aembit."
resource: https://docs.aembit.io/user-guide/access-policies/client-workloads/identification/kubernetes-pod-name-prefix/
interface: web-ui
tags: ["kubernetes", "identification", "client-workload", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Kubernetes Pod Name Prefix


In Kubernetes environments, pods are often dynamically created and assigned unique names. The Kubernetes Pod Name Prefix identification method allows you to target a group of pods belonging to the same deployment by specifying the common prefix of their names. This is particularly useful for managing access for deployments with multiple replicas or deployments that are frequently scaled up or down.

## Applicable Deployment Type

This method is suitable for Edge-based deployments.

## Configuration

### Aembit Cloud

1. Create a new Client Workload.
2. Choose **Kubernetes Pod Name Prefix** for client identification.
3. In the **Value** field, enter the desired pod name prefix. This is typically the name of your deployment.

#### Finding the Pod Name Prefix:

* Use the `kubectl get pods` command to list all pods in your cluster.
* Identify the pods belonging to your target deployment. Their names will share a common prefix.
* Use this common prefix as the Value in the Client Workload configuration.

#### Uniqueness

Ensure that the chosen prefix is unique enough to avoid unintentionally matching pods from other deployments. Please consult the [Client Workload multiple identifiers](client-workload-multiple-ids.md) documentation to enhance uniqueness.

## Related

**Compatible trust providers**

* [Kubernetes Service Account](../../trust-providers/kubernetes-service-account-trust-provider.md)
