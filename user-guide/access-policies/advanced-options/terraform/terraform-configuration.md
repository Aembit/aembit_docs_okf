---
type: how-to
title: "Configuration with Terraform"
description: "How to use the Aembit Terraform Provider to configure Aembit Cloud resources"
resource: https://docs.aembit.io/user-guide/access-policies/advanced-options/terraform/terraform-configuration/
interface: web-ui
tags: [terraform, advanced-option, access-policy]
timestamp: 2026-06-23T16:24:12-07:00
type_inferred: true
---

# Configuration with Terraform

Aembit has released a Terraform Provider in the [Terraform Registry](https://registry.terraform.io/providers/Aembit/aembit/latest) that enables users to configure Aembit Cloud resources in an automated manner.

## Configuration

[Section titled “Configuration”](#configuration)

Configuring the Aembit Terraform provider requires two steps:

1. Create or update the Terraform configuration to include the Aembit provider.

2. Specify the Aembit provider authentication configuration.

   a. Aembit recommends using Aembit integrated authentication for dynamic retrieval of the Aembit API Access Token. This can be done by specifying the Aembit Edge SDK Client ID from an appropriately configured Aembit Trust Provider.

   b. For development and testing purposes, users can specify an Aembit Tenant ID and Token for short-term access.

Additional details for how to perform each of these steps can be found in the [Provider Documentation](https://registry.terraform.io/providers/Aembit/aembit/latest/docs) section of the Aembit Terraform provider page.

## Resources and Data Sources

[Section titled “Resources and Data Sources”](#resources-and-data-sources)

The Aembit [Terraform Provider](https://registry.terraform.io/providers/Aembit/aembit/latest) enables users to create, update, import, and delete Aembit Cloud resources using terraform manually or via CI/CD workflows. For example, users can configure GitHub Actions or Terraform Workspaces to utilize the Aembit Terraform provider and manage Aembit Cloud resources on demand to best serve their Workload purposes.

Detailed instructions for using the Aembit Terraform Provider can be found in the [Terraform Registry](https://registry.terraform.io/providers/Aembit/aembit/latest/docs).

You can also manage Resource Sets as code with the `aembit_resource_set` resource, and assign or filter entities by Resource Set with the `resource_set_id` argument. Omitting `resource_set_id` uses the Default Resource Set, so existing configurations keep working unchanged. For more on managing Resource Sets at scale, see [Scaling Aembit with Terraform](../../../../get-started/concepts/scaling-terraform.md).
