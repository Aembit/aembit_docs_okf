---
type: how-to
title: "Terraform Cloud Workspace ID"
description: "How to identify Terraform Cloud Workloads using the workspace ID from a Terraform Cloud Identity Token in Aembit"
resource: https://docs.aembit.io/user-guide/access-policies/client-workloads/identification/terraform-cloud-id-token-workspace-id/
interface: web-ui
tags: ["cicd", "identification", "client-workload", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Terraform Cloud Workspace ID

This page explains how to use the **Terraform Cloud ID Token Workspace ID** identifier to uniquely identify Terraform workloads running on **Terraform Cloud (TFC)** using a Terraform Cloud ID Token.

## Understanding the Terraform Cloud ID token workspace ID

When Terraform Cloud executes runs, it can issue an [OIDC-compliant identity token](https://developer.hashicorp.com/terraform/enterprise/workspaces/dynamic-provider-credentials/workload-identity-tokens) that includes an `terraform_workspace_id` claim. This value uniquely identifies the Terraform Cloud workspace under which the workload runs. Aembit uses this value to associate a Terraform run with a specific Client Workload.

For example, an workspace ID might look like: `ws-mbsd5E3Ktt5Rg2Xm`

## Applicable deployment type

Aembit supports this identifier only when you use the [Aembit Terraform provider](https://registry.terraform.io/providers/Aembit/aembit/latest).

## Create a Client Workload with a Terraform Cloud ID Token identifier

To configure a Client Workload using the Terraform Cloud ID Token identifier, follow these steps:

1. Log into your Aembit Tenant.

2. Click **Client Workloads** in the left nav pane.

3. Click **New**, revealing the **Client Workload** pop out menu.

4. Enter the **Name** and optional **Description** for the Client Workload.

5. Under **Client Identification**, select **Terraform Cloud ID Token Workspace ID**.

   For **Value**, enter the Terraform Cloud Workspace ID associated with the workload.

   For example: `ws-mbsd5E3Ktt5Rg2Xm`

6. Click **Save**.

   Aembit displays the new Client Workload on the **Client Workloads** page.

## Find Terraform cloud workspace ID

1. Log into [Terraform Cloud](https://app.terraform.io).
2. Choose your organization.
3. In the left navigation menu, click **Workspaces**.
4. Choose your workspace.
5. Open **Settings** > **General** for the workspace; the **Workspace ID** (prefixed `ws-`) appears there.

## Related

**Compatible trust providers**

* [Terraform Cloud Identity Token](../../trust-providers/terraform-cloud-identity-token-trust-provider.md)
