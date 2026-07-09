---
type: how-to
title: "Kubernetes Service Account Name"
description: "How to identify Kubernetes workloads using the Kubernetes Service Account Name within Aembit"
resource: https://docs.aembit.io/user-guide/access-policies/client-workloads/identification/kubernetes-service-account-name/
interface: web-ui
tags: [kubernetes, identification, client-workload, access-policy]
timestamp: 2025-06-17T17:07:06-07:00
type_inferred: true
---

# Kubernetes Service Account Name

This page explains how to use the **Kubernetes Service Account Name** identifier to uniquely identify workloads deployed on **Kubernetes**.

## Understanding the Kubernetes service account name identifier

[Section titled “Understanding the Kubernetes service account name identifier”](#understanding-the-kubernetes-service-account-name-identifier)

In Kubernetes, service accounts provide an identity for processes that run in a pod. You can assign each pod a service account, and the pod uses this account when it interacts with the Kubernetes API or other services.

Using the **service account name** as an identifier is useful when you want to manage Access Policies tied to the identity of workloads, rather than their namespace or pod name.

## Applicable deployment type

[Section titled “Applicable deployment type”](#applicable-deployment-type)

Aembit supports the Kubernetes Service Account Name identification method for Edge-based deployments on [Kubernetes](../../../deploy-install/kubernetes/kubernetes.md).

## Create a Client Workload with a Kubernetes service account name identifier

[Section titled “Create a Client Workload with a Kubernetes service account name identifier”](#create-a-client-workload-with-a-kubernetes-service-account-name-identifier)

To configure a Client Workload with a Kubernetes Service Account Name identifier, follow these steps:

1. Log into your Aembit Tenant.

2. Click **Client Workloads** in the left nav pane.

3. Click **New**, revealing the **Client Workload** pop out menu.

4. Enter the **Name** and optional **Description** for the Client Workload.

5. Under **Client Identification**, select **Kubernetes Service Account Name**.

   For **Value**, enter the name of the Kubernetes Service Account used by the workload.

   For example, if your service account is `app-sa`, enter that in the **Value** field.

   If you don’t know the service account name or how to find it, see [Find Kubernetes Service Account Name](#find-kubernetes-service-account-name).

6. Click **Save**.

   Aembit displays the new Client Workload on the **Client Workloads** page.

Client Workload identifier uniqueness

When you identify a Client Workload using a single identifier such as this one in a complex environment, this identifier may not always provide sufficient uniqueness.

To avoid Aembit unintentionally matching something beyond what it’s intended to match, Aembit recommends that you set additional identifiers to compliment this one, creating a Client Workload identity that’s unique across your environment.

See [Using multiple Client Workload identifiers](client-workload-multiple-ids.md) for guidance on combining this identifier with more specific identifiers such as those on the [Client Workload Identifiers overview](overview.md) page, which includes vendor- and technology-specific identifiers.

## Find Kubernetes service account name

[Section titled “Find Kubernetes service account name”](#find-kubernetes-service-account-name)

To find the Kubernetes Service Account Name used by a workload, follow these steps:

Note

You can also find the service account name in the Kubernetes deployment YAML files, Helm chart values, or other IaC configurations.

Be sure to enter the service account name exactly as it appears in your Kubernetes cluster.

1. Use the command: `kubectl get serviceaccount -n <namespace>`

2. Locate the service account associated with your workload in the output.

3. Use the value in the `NAME` column as the identifier in your Aembit configuration.

## Related

**Compatible trust providers**

* [Kubernetes Service Account](../../trust-providers/kubernetes-service-account-trust-provider.md)
