---
type: how-to
title: "Configure multiple Credential Providers with Aembit's Terraform Provider"
description: "How to configure multiple Credential Providers to map to an Aembit Terraform Provider"
resource: https://docs.aembit.io/user-guide/access-policies/credential-providers/advanced-options/multiple-credential-providers-terraform/
interface: web-ui
tags: ["advanced-option", "credential-provider", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Configure multiple Credential Providers with Aembit's Terraform Provider

Aembit supports users who would like to use the Aembit Terraform Provider to manage their Aembit resources, while also supporting single and multiple Credential Providers per Access Policy. The Aembit Terraform Provider enables you to perform Create, Read, Update and Delete (CRUD) operations on these Aembit resources using Terraform directly, or via a CI/CD workflow.

> **Note**
>
> These instructions assume you already have configured the Aembit Terraform Provider. If you have not already performed this configuration, please refer to the [Configuration with Terraform](../../advanced-options/terraform/terraform-configuration.md) page to configure the Aembit Terraform Provider before continuing on this page.

## Configure an Access Policy with multiple Credential providers

To configure your Aembit Access Policies with multiple Credential Providers with the `AccountName` mapping type:

1. Go to your Terraform configuration file(s).

2. In your configuration file, locate the `resource "aembit_access_policy"` section(s). They should look like the example shown below.

   ```hcl
   resource "aembit_access_policy" "test_policy" {
       name = "TF First Policy"
       is_active = true
       client_workload = aembit_client_workload.first_client.id
       trust_providers = [
           aembit_trust_provider.azure1.id,
           aembit_trust_provider.azure2.id
       ]
       access_conditions = [
           aembit_access_condition.wiz.id
       ]
       credential_provider = aembit_credential_provider.<*resource_name*>.id,
       server_workload = aembit_server_workload.first_server.id
   }
   ```

   In the preceding example, notice in the highlighted line for `credential_provider`. Because there is only one Credential Provider configured, this signifies that only one Credential Provider is currently configured for the Access Policy.

3. To add additional Credential Providers to your configuration, go to the `aembit_access_policy` resource in your Terraform configuration file that you want to update and locate the `credential_provider` line.

4. Change the `credential_provider` property to `credential_providers` so you may add multiple Credential Providers.

5. Add your Credential Providers to this section using the following format:

   ```hcl
       credential_providers = [{
     credential_provider_id = aembit_credential_provider.<*resource1_name*>.id,
     mapping_type = "AccountName",
     account_name = "account_name_1"
       }, {
     credential_provider_id = aembit_credential_provider.<*resource2_name*>.id,
           mapping_type = "AccountName",
           account_name = "account_name_2"
       }, {
     credential_provider_id = aembit_credential_provider.<*resource3_name*>.id,
           mapping_type = "AccountName",
           account_name = "account_name_3"
       }]
   }
   ```

   Where:

   * `credential_provider_id` - The Credential Provider ID.
   * `mapping_type` - The Credential Provider mapping type.
   * `account_name` - The account name to trigger on for using this Credential Provider if the `mapping_type` value is `AccountName`.

6. When you have finished adding all of your Credential Providers to the Aembit Terraform Provider configuration file, your `aembit_access_policy` resource section should look similar to the example shown below.

   ```hcl
   resource "aembit_access_policy" "multi_cp_second_policy" {
       is_active = true
       name = "TF Multi CP Second Policy"
       client_workload = aembit_client_workload.second_client.id
       credential_providers = [{
     credential_provider_id = aembit_credential_provider.<*resource1_name*>.id,
     mapping_type = "AccountName",
     account_name = "account_name_1"
       }, {
     credential_provider_id = aembit_credential_provider.<resource2_name*>.id,
           mapping_type = "AccountName",
           account_name = "account_name_2"
       }, {
     credential_provider_id = aembit_credential_provider.<resource3_name*>.id,
           mapping_type = "AccountName",
           account_name = "account_name_3"
       }]
       server_workload = aembit_server_workload.first_server.id
   }
   ```

### Multiple Credential Provider examples

The following examples use `HttpHeader` and `HttpBody` Mapping Types to show multiple Credential Providers:

#### HttpHeader Example

```hcl
resource "aembit_access_policy" "multi_cp_httpheader" {
    is_active = true
    name = "TF Multi CP HTTP Header"
    client_workload = aembit_client_workload.first_client.id
    credential_providers = [{
  credential_provider_id = aembit_credential_provider.<*resource1_name*>.id,
  mapping_type = "HttpHeader",
  header_name = "X-Sample-Header-name-1",
  header_value = "X-Sample-Header-value-1"
    }, {
  credential_provider_id = aembit_credential_provider.<*resource2_name*>.id,
  mapping_type = "HttpHeader",
  header_name = "X-Sample-Header-name-2",
  header_value = "X-Sample-Header-value-2"
    }]
    server_workload = aembit_server_workload.first_server.id
}
```

Where:

* `credential_provider_id` - The Credential Provider ID.
* `mapping_type` - The Credential Provider mapping type.
* `header_name` - The HTTP Header name for which a matching value will trigger this Credential Provider to be used.
* `header_value` - The HTTP Header value for which a matching value will trigger this Credential Provider to be used.

#### HttpBody Example

```hcl
resource "aembit_access_policy" "multi_cp_httpbody" {
    is_active = true
    name = "TF Multi CP HTTP Body"
    client_workload = aembit_client_workload.first_client.id
    credential_providers = [{
  credential_provider_id = aembit_credential_provider.<*resource1_name*>.id,
  mapping_type = "HttpBody",
  httpbody_field_path = "x_sample_httpbody_field_path_1",
  httpbody_field_value = "x_sample_httpbody_field_value_1"
    }, {
  credential_provider_id = aembit_credential_provider.<*resource2_name*>.id,
  mapping_type = "HttpBody",
  httpbody_field_path = "x_sample_httpbody_field_path_2",
  httpbody_field_value = "x_sample_httpbody_field_value_2"
    }]
    server_workload = aembit_server_workload.first_server.id
}
```

Where:

* `credential_provider_id` - The Credential Provider ID.
* `mapping_type` - The Credential Provider mapping type.
* `httpbody_field_path` - The JSON path to a value that triggers this Credential Provider to be used. Note that the `HttpBody` mapping type requires JSON HTTP body content, and this parameter must be specified in JSON path notation.
* `httpbody_field_value` - The JSON path to a value which triggers this Credential Provider to be used.

> **Note**
>
> In these two examples, you can see that different fields need to be configured, based on the `mapping_type` specified in the configuration file.
