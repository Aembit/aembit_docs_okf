---
type: explanation
title: "Scaling Aembit with Terraform"
description: "Description of how to scale with the Aembit Terraform provider"
resource: https://docs.aembit.io/get-started/concepts/scaling-terraform/
tags: ["concept"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Scaling Aembit with Terraform

Aembit supports scalable, repeatable infrastructure-as-code workflows through its [official **Terraform provider**](https://registry.terraform.io/providers/Aembit/aembit/latest). By managing Aembit resources declaratively in code, you can automate onboarding, ensure consistent policies across environments, and scale access controls alongside your infrastructure.

This guide explains how the Aembit Terraform Provider works and how to use it to scale Aembit in production environments.

## Why Use Terraform with Aembit?

Terraform gives you the ability to:

* **Codify access policies and workload identity configuration**
* **Version control changes** to your identity and access infrastructure
* **Apply changes consistently** across staging, production, and multicloud environments
* **Automate onboarding** for new workloads, trust providers, and credential integrations

This helps reduce manual steps, eliminate configuration drift, and ensure your access policies are reproducible and reviewable.

## What Can You Manage?

The Aembit Terraform Provider supports all core Aembit resources:

| Resource Type        | Terraform Support                     |
| -------------------- | ------------------------------------- |
| Trust Providers      | ✅ Create and configure                |
| Client Workloads     | ✅ Manage identity matching            |
| Server Workloads     | ✅ Define endpoints, auth              |
| Credential Providers | ✅ Integrate secrets/tokens            |
| Access Policies      | ✅ Authorize workload access           |
| Access Conditions    | ✅ Enforce dynamic controls            |
| Resource Sets        | ✅ Create, manage, and assign entities |
| Roles & Permissions  | ✅ Assign fine-grained access          |

This full coverage enables you to declare your Aembit configuration as code, just like cloud resources or Kubernetes objects.

## Managing Resource Sets at scale

When you manage many environments or tenants, defining Resource Sets by hand in the Aembit UI doesn’t scale well. The Aembit Terraform Provider lets you define Resource Sets as code and assign entities to them, so you can manage a large footprint consistently and repeatably.

* Use the `aembit_resource_set` resource to manage the lifecycle of a Resource Set.
* Set the `resource_set_id` argument on a resource to assign that entity to a specific Resource Set.
* Set the `resource_set_id` argument on a data source to filter results to a specific Resource Set.
* If you omit `resource_set_id`, Aembit assigns the entity to the Default Resource Set, so existing configurations keep working without changes.

For the arguments, attributes, and import syntax of each resource and data source, see the [Aembit provider documentation](https://registry.terraform.io/providers/Aembit/aembit/latest/docs) in the Terraform Registry.

> **Destroying a Resource Set deletes everything in it**
>
> Destroying an `aembit_resource_set` deletes every entity the Resource Set contains, the same as [deleting it in the UI](../../user-guide/administration/resource-sets/delete-resource-set.md). This can leave your Terraform state out of sync and stop associated Agent Proxy and Edge components from working.

## How the Terraform Provider Works

1. **Authenticate** with your Aembit Tenant by providing an access token.
2. **Declare resources** like workloads, policies, and credential providers in `.tf` files.
3. **Run `terraform apply`** to push the desired state to Aembit.
4. Aembit **provisions or updates** the corresponding resources in your tenant.

Example provider block:

```hcl
provider "aembit" {
  token = var.aembit_api_token
  tenant_id = var.aembit_tenant_id
}
```
