---
type: how-to
title: "Kubernetes Namespace"
description: "How to identify Kubernetes workloads using the Kubernetes Namespace within Aembit"
resource: https://docs.aembit.io/user-guide/access-policies/client-workloads/identification/kubernetes-namespace/
interface: web-ui
tags: ["kubernetes", "identification", "client-workload", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Kubernetes Namespace

This page explains how to use the **Kubernetes Namespace** identifier to uniquely identify workloads deployed on **Kubernetes**.

## Understanding the Kubernetes Namespace identifier

Namespaces in Kubernetes provide a way to divide cluster resources between multiple users or applications. They’re commonly used to group related workloads and manage resource allocation and access boundaries. Using a namespace as an identifier is useful when you want to manage Access Policies for all workloads within a specific namespace.

## Applicable deployment type

Aembit supports the Kubernetes Namespace identification method for Edge-based deployments on [Kubernetes](../../../deploy-install/kubernetes/kubernetes.md).

## Create a Client Workload with a Kubernetes Namespace identifier

To configure a Client Workload with a Kubernetes Namespace identifier, follow these steps:

1. Log into your Aembit Tenant.

2. Click **Client Workloads** in the left nav pane.

3. Click **New**, revealing the **Client Workload** pop out menu.

4. Enter the **Name** and optional **Description** for the Client Workload.

5. Under **Client Identification**, select **Kubernetes Namespace**.

   For **Value**, enter the name of the Kubernetes Namespace where the workload is running.

   For example, if your namespace is `backend-services`, enter that in the **Value** field.

   If you don’t know the namespace or how to find it, see [Find Kubernetes Namespace](#find-kubernetes-namespace).

6. Click **Save**.

   Aembit displays the new Client Workload on the **Client Workloads** page.

> **Client Workload identifier uniqueness**
>
> When you identify a Client Workload using a single identifier such as this one in a complex environment, this identifier may not always provide sufficient uniqueness.
>
> To avoid Aembit unintentionally matching something beyond what it’s intended to match, Aembit recommends that you set additional identifiers to compliment this one, creating a Client Workload identity that’s unique across your environment.
>
> See [Using multiple Client Workload identifiers](client-workload-multiple-ids.md) for guidance on combining this identifier with more specific identifiers such as those on the [Client Workload Identifiers overview](overview.md) page, which includes vendor- and technology-specific identifiers.

## Find Kubernetes namespace

To find the Kubernetes Namespace of a workload, follow these steps:

> **Note**
>
> You can also find the namespace in the Kubernetes deployment YAML files, Helm chart values, or other IaC configurations that define where you deploy workloads.
>
> Be sure to enter the namespace exactly as it appears in your Kubernetes cluster configuration.

1. Use the command: `kubectl get pods --all-namespaces`.

2. Locate the workload you want to identify in the output.

3. Note the value under the `NAMESPACE` column—this is the value to use in your Aembit configuration.

## Related

**Compatible trust providers**

* [Kubernetes Service Account](../../trust-providers/kubernetes-service-account-trust-provider.md)
