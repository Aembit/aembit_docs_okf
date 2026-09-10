---
type: how-to
title: "Kubernetes Service Account UID"
description: "How to identify Kubernetes workloads using the Kubernetes Service Account UID within Aembit"
resource: https://docs.aembit.io/user-guide/access-policies/client-workloads/identification/kubernetes-service-account-uid/
interface: web-ui
tags: ["kubernetes", "identification", "client-workload", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Kubernetes Service Account UID

This page explains how to use the **Kubernetes Service Account UID** identifier to uniquely identify workloads deployed on **Kubernetes**.

## Understanding the Kubernetes service account UID identifier

In Kubernetes, service accounts provide an identity for processes that run in a pod. You can assign each pod a service account, and the pod uses this account when it interacts with the Kubernetes API or other services.

Using the **service account UID** as an identifier is useful when you want to manage Access Policies tied to the unique identity of workloads, rather than their namespace, pod name.

## Applicable deployment type

Aembit supports the Kubernetes Service Account UID identification method for Edge-based deployments on [Kubernetes](../../../deploy-install/kubernetes/kubernetes.md).

## Create a Client Workload with a Kubernetes service account UID identifier

To configure a Client Workload with a Kubernetes Service Account UID identifier, follow these steps:

1. Log into your Aembit Tenant.

2. Click **Client Workloads** in the left nav pane.

3. Click **New**, revealing the **Client Workload** pop out menu.

4. Enter the **Name** and optional **Description** for the Client Workload.

5. Under **Client Identification**, select **Kubernetes Service Account UID**.

   For **Value**, enter the UID of the Kubernetes Service Account used by the workload.

   For example, if the UID is `abc12345-6789-def0-1234-56789abcdef0`, enter that in the **Value** field.

   If you don’t know the UID or how to find it, see [Find Kubernetes Service Account UID](#find-kubernetes-service-account-uid).

6. Click **Save**.

   Aembit displays the new Client Workload on the **Client Workloads** page.

## Find Kubernetes service account UID

To find the Kubernetes Service Account UID used by a workload, follow these steps:

1. Use the command: `kubectl get serviceaccount -n <namespace>` to find the service account name.

2. Then run: `kubectl get serviceaccount <serviceaccountname> -n <namespace> -o yaml`

3. Locate the `metadata.uid` field in the output. Use this value as the identifier in your Aembit configuration.

## Related

**Compatible trust providers**

* [Kubernetes Service Account](../../trust-providers/kubernetes-service-account-trust-provider.md)
