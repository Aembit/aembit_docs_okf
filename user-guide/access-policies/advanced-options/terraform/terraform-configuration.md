---
type: how-to
title: "Configuration with Terraform"
description: "How to use the Aembit Terraform Provider to configure Aembit Cloud resources"
resource: https://docs.aembit.io/user-guide/access-policies/advanced-options/terraform/terraform-configuration/
interface: web-ui
tags: ["terraform", "advanced-option", "access-policy"]
timestamp: 2026-11-10T19:33:34-08:00
---

# Configuration with Terraform

Aembit has released a Terraform Provider in the [Terraform Registry](https://registry.terraform.io/providers/Aembit/aembit/latest) that enables users to configure Aembit Cloud resources in an automated manner.

## Configuration

Configuring the Aembit Terraform provider requires two steps:

1. Create or update the Terraform configuration to include the Aembit provider.

2. Specify the Aembit provider authentication configuration.

   * Aembit recommends using Aembit integrated authentication for dynamic retrieval of the Aembit API Access Token. To do this, specify the Aembit Edge SDK Client ID from an appropriately configured Aembit Trust Provider.
   * For development and testing purposes, you can specify an Aembit Tenant ID and Token for short-term access.

The [Provider Documentation](https://registry.terraform.io/providers/Aembit/aembit/latest/docs) section of the Aembit Terraform provider page has additional details for each of these steps.

## Resources and data sources

The Aembit [Terraform Provider](https://registry.terraform.io/providers/Aembit/aembit/latest) enables you to create, update, import, and delete Aembit Cloud resources using Terraform manually or through CI/CD workflows. For example, you can configure GitHub Actions or Terraform Workspaces to use the Aembit Terraform provider and manage Aembit Cloud resources on demand to best serve your Workload purposes.

The [Terraform Registry](https://registry.terraform.io/providers/Aembit/aembit/latest/docs) has detailed instructions for using the Aembit Terraform Provider.

You can also manage Resource Sets as code with the `aembit_resource_set` resource, and assign or filter entities by Resource Set with the `resource_set_id` argument. Omitting `resource_set_id` uses the Default Resource Set, so existing configurations keep working unchanged. For more on managing Resource Sets at scale, see [Scaling Aembit with Terraform](../../../../get-started/concepts/scaling-terraform.md).

## Use Terraform with the Edge SDK

Terraform and the [Edge SDK](../../../../dev-guide/sdk/edge/overview.md) cover different stages of the same Access Policy. Terraform provisions the Aembit configuration, such as Trust Providers, Client Workloads, and Access Policies. Your program then uses the Edge SDK to retrieve credentials at runtime through that configuration.

The program authenticates with the Edge SDK Client ID of its Trust Provider. For most Trust Provider types the Edge SDK supports, the `aembit_trust_provider` resource’s read-only `client_id` attribute holds that value, so you can pass it from Terraform outputs to your program’s configuration. For the AWS Metadata Service, AWS Role, and Azure Instance Metadata Service types, the attribute is null, so copy the Edge SDK Client ID from the Aembit Tenant UI. For an example in each environment, see the Edge SDK integration guides, such as [Use the Edge SDK in Terraform Cloud](../../../../dev-guide/sdk/edge/integrations/terraform-cloud.md).
