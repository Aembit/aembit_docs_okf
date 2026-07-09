---
type: how-to
title: "Terraform Cloud Organization ID"
description: "How to identify Terraform Cloud Workloads using the organization ID from a Terraform Cloud Identity Token in Aembit"
resource: https://docs.aembit.io/user-guide/access-policies/client-workloads/identification/terraform-cloud-id-token-organization-id/
interface: web-ui
tags: [cicd, identification, client-workload, access-policy]
timestamp: 2025-07-07T06:58:26-07:00
type_inferred: true
---

# Terraform Cloud Organization ID

This page explains how to use the **Terraform Cloud ID Token Organization ID** identifier to uniquely identify Terraform workloads running on **Terraform Cloud (TFC)** using a Terraform Cloud ID Token.

## Understanding the Terraform Cloud ID token organization ID

[Section titled “Understanding the Terraform Cloud ID token organization ID”](#understanding-the-terraform-cloud-id-token-organization-id)

When Terraform Cloud executes runs, it can issue an [OIDC-compliant identity token](https://developer.hashicorp.com/terraform/enterprise/workspaces/dynamic-provider-credentials/workload-identity-tokens) that includes an `terraform_organization_id` claim. This value uniquely identifies the Terraform Cloud organization under which the workload runs. Aembit uses this value to associate a Terraform run with a specific Client Workload.

For example, an organization ID might look like: `org-GRNbCjYNpBB6NEH9`

## Applicable deployment type

[Section titled “Applicable deployment type”](#applicable-deployment-type)

Aembit supports this identifier only when you use the [Aembit Terraform provider](https://registry.terraform.io/providers/Aembit/aembit/latest).

## Create a Client Workload with a Terraform Cloud ID Token identifier

[Section titled “Create a Client Workload with a Terraform Cloud ID Token identifier”](#create-a-client-workload-with-a-terraform-cloud-id-token-identifier)

To configure a Client Workload using the Terraform Cloud ID Token identifier, follow these steps:

1. Log into your Aembit Tenant.

2. Click **Client Workloads** in the left nav pane.

3. Click **New**, revealing the **Client Workload** pop out menu.

4. Enter the **Name** and optional **Description** for the Client Workload.

5. Under **Client Identification**, select **Terraform Cloud ID Token Organization ID**.

   For **Value**, enter the Terraform Cloud Organization ID associated with the workload.

   For example: `org-GRNbCjYNpBB6NEH9`

6. Click **Save**.

   Aembit displays the new Client Workload on the **Client Workloads** page.

Client Workload identifier uniqueness

When you identify a Client Workload using a single identifier such as this one in a complex environment, this identifier may not always provide sufficient uniqueness.

To avoid Aembit unintentionally matching something beyond what it’s intended to match, Aembit recommends that you set additional identifiers to compliment this one, creating a Client Workload identity that’s unique across your environment.

See [Using multiple Client Workload identifiers](client-workload-multiple-ids.md) for guidance on combining this identifier with more specific identifiers such as those on the [Client Workload Identifiers overview](overview.md) page, which includes vendor- and technology-specific identifiers.

## Find Terraform cloud organization ID

[Section titled “Find Terraform cloud organization ID”](#find-terraform-cloud-organization-id)

1. Log into [Terraform Cloud](https://app.terraform.io).
2. Choose your organization.
3. In the left navigation menu, click **Settings**.
4. Under **General Settings**, you’ll find the **Organization ID** at the top of the page.

## Related

**Compatible trust providers**

* [Terraform Cloud Identity Token](../../trust-providers/terraform-cloud-identity-token-trust-provider.md)
