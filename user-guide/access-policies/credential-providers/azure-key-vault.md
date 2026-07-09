---
type: how-to
title: "Create an Azure Key Vault Credential Provider"
description: "How to create and use the Azure Key Vault Credential Provider"
resource: https://docs.aembit.io/user-guide/access-policies/credential-providers/azure-key-vault/
interface: web-ui
tags: [credential-provider, access-policy]
timestamp: 2026-06-26T13:11:59-07:00
type_inferred: true
---

# Create an Azure Key Vault Credential Provider

The *Azure Key Vault Credential Provider* uses the [Azure Entra Federation Credential Provider Integration](integrations/azure-entra-federation.md) to enable secure, policy-driven retrieval of static credentials stored in your Azure Key Vault. This includes API keys, usernames, and passwords. This integration allows you to leverage Aembit’s conditional access controls and centralized auditing for secrets managed in Azure, supporting both public and private network access scenarios.

## Supported credential types and workloads

[Section titled “Supported credential types and workloads”](#supported-credential-types-and-workloads)

| Credential Value Type | Supported Workloads & Protocols                       |
| --------------------- | ----------------------------------------------------- |
| Single Value          | HTTP (Bearer, Header, Query Parameter)                |
| Username/Password     | HTTP (Basic Auth), Redshift, PostgreSQL, MySQL, Redis |

Caveats

* Azure Key Vault only allows one value per secret.\
  For Username/Password credential types, you must create two separate secrets in Azure Key Vault (one for the username and one for the password).
* Aembit only supports string-based secrets (not certificates or arbitrary data).
* Doesn’t manage secret lifecycle (creation, rotation, deletion)—only retrieval.
* Aembit maps each Azure Entra Federation Credential Provider Integration to a single Key Vault instance.

## Prerequisites

[Section titled “Prerequisites”](#prerequisites)

You must have the following to create an Azure Key Vault Credential Provider:

* Access to an Azure subscription with permissions to create and manage an Azure Key Vault instance with secrets and appropriate access policies or Role-Based Access Control (RBAC) roles assigned

* A completed [Azure Entra Federation Credential Provider Integration](integrations/azure-entra-federation.md)

* An Azure Key Vault with secrets you want to manage via Aembit

* Access to your Aembit Tenant with permissions to create Credential Providers and manage Access Policies

* For Private Network Access: Aembit Agent Proxy v1.26+ deployed in your environment

* Terraform only:

  * You have Terraform installed.

  * You have the [Aembit Terraform Provider](https://registry.terraform.io/providers/Aembit/aembit/latest) configured.

## Accessing Azure Key Vault on private networks

[Section titled “Accessing Azure Key Vault on private networks”](#accessing-azure-key-vault-on-private-networks)

For Key Vault instances on private networks (such as an Azure Virtual Network), enable **Private Network Access** during configuration to allow your colocated Agent Proxy to handle credential retrieval directly.

For details on when to use Private Network Access, how it works, and troubleshooting, see [Private Network Access for Credential Providers](private-network-access.md).

Version requirement

Private Network Access for Azure Key Vault requires Agent Proxy 1.26 or later.

## Configure Azure Key Vault for Aembit

[Section titled “Configure Azure Key Vault for Aembit”](#configure-azure-key-vault-for-aembit)

To configure Azure Key Vault for Aembit, follow these steps:

1. Go to **Create a resource → Key Vault → Create** in the Azure portal.

2. Select your **Subscription** and **Resource Group**.

3. Enter a **Key Vault name** and select your **Region**.

4. Choose the **Permission Model** (Azure RBAC or Vault access policy).

5. Grant access to the federated app using one of the following methods:

   * Azure RBAC

     1. Open the Key Vault and go to **Access control (IAM)**.
     2. Click **Add role assignment**.
     3. Select **Key Vault Secrets User**.
     4. Click **Next**, then **Select members**.
     5. Search for and select your federated app.
     6. Click **Select**, then **Review + assign** twice.

   * Vault Access Policy

     1. Open the Key Vault and go to **Access policies**.
     2. Click **+ Create**.
     3. Under **Secret permissions**, check **Get** and **List**.
     4. Click **Next**, search for your federated app, and select it.
     5. Click **Next** twice, then **Create**.

6. Add secrets to the Key Vault:

   1. Go to **Objects → Secrets**.
   2. Click **+ Generate/Import**.
   3. Enter a name and value for the secret.
   4. Click **Create**.\
      For username/password, **you must** create two separate secrets.

   Security best practice

   Restrict secret access to only the federated app and only for the required secrets. Use least-privilege principles.

## Create an Azure Key Vault Credential Provider

[Section titled “Create an Azure Key Vault Credential Provider”](#create-an-azure-key-vault-credential-provider)

To create an Azure Key Vault Credential Provider, follow these steps:

1. Log into your Aembit Tenant, and go to **Credential Providers** in the left sidebar.

2. (Optional) In the top right corner, select the [Resource Set](../../administration/resource-sets/overview.md) that you want this Credential Provider to reside.

3. Click **+ New**, which displays the Credential Provider pop out menu.

4. Enter a **Name** and optional **Description**.

5. Under **Credential Type**, select **Azure Key Vault Secret Value**, revealing more fields.

6. Fill out the remaining fields:

   * **Select CP Integration** - Select the Azure Entra Federation integration you’ve already configured.

     Note

     If the **Select CP Integration** dropdown menu is empty, you either:

     * May not have any Azure Entra Federation integrations configured yet. See [Azure Entra Federation](integrations/azure-entra-federation.md) to create one.

     * May need to change Resource Sets.

   * **Credential Value Type** - Select the type of credential (Single Value or Username/Password).

   * **Secret Name** - Select or enter the name of the secret in Azure Key Vault. If you enabled **Fetch Secret Names** in the Azure Entra Federation integration, the secret names automatically load in a dropdown for easier selection. If **Fetch Secret Names** wasn’t enabled, manually enter the secret name. For Username/Password, enter two separate secret names (one for username, one for password).

   * **Private Network Access** - Enable this if your Key Vault exists in a private network or is only accessible from your Edge deployment. See [Accessing Azure Key Vault on private networks](#accessing-azure-key-vault-on-private-networks) for details.

   ![Azure Key Vault Credential Provider form](https://docs.aembit.io/_astro/cp-azure-key-vault.HjPIpN4h_NQOEX.webp)

7. Click **Save**.

   Aembit displays the new Credential Provider in the list of Credential Providers.

## Configure Azure Key Vault CP using the Aembit Terraform provider

[Section titled “Configure Azure Key Vault CP using the Aembit Terraform provider”](#configure-azure-key-vault-cp-using-the-aembit-terraform-provider)

To configure an Azure Key Vault Credential Provider using the [Aembit Terraform Provider](https://registry.terraform.io/providers/Aembit/aembit/latest), follow the steps in this section.

1. Ensure you have completed the [Configure Azure Key Vault for Aembit](#configure-azure-key-vault-for-aembit) steps.

2. Ensure you have created an [Azure Entra Federation integration](integrations/azure-entra-federation.md) and have its integration ID.

   You can obtain the integration ID by running:

   ```shell
   terraform state show aembit_credential_provider_integration.azure_entra_federation
   ```

3. Create a new Terraform configuration file (such as `azure-kv-cp.tf`) based on your credential type:

   * Single Value

     ```hcl
     provider "aembit" {
     }


     resource "aembit_credential_provider" "azure_kv" {
       name      = "<Desired Credential Provider Name>"
       is_active = true
       azure_key_vault_value = {
         credential_provider_integration_id = "<Azure Entra Federation Integration ID>"
         secret_name_1                      = "<Secret Name in Azure Key Vault>"
         private_network_access             = false
       }
     }
     ```

     Example Terraform resource file for API Key

     ```hcl
     provider "aembit" {
     }


     resource "aembit_credential_provider" "azure_kv_api_key" {
       name      = "Azure KV API Key"
       is_active = true
       azure_key_vault_value = {
         credential_provider_integration_id = "a1b2c3d4-e5f6-7890-abcd-ef1234567890"
         secret_name_1                      = "api-key-secret"
         private_network_access             = false
       }
     }
     ```

   * Username/Password

     ```hcl
     provider "aembit" {
     }


     resource "aembit_credential_provider" "azure_kv" {
       name      = "<Desired Credential Provider Name>"
       is_active = true
       azure_key_vault_value = {
         credential_provider_integration_id = "<Azure Entra Federation Integration ID>"
         secret_name_1                      = "<Username Secret Name in Azure Key Vault>"
         secret_name_2                      = "<Password Secret Name in Azure Key Vault>"
         private_network_access             = false
       }
     }
     ```

     Example Terraform resource file for Database Credentials

     ```hcl
     provider "aembit" {
     }


     resource "aembit_credential_provider" "azure_kv_db_creds" {
       name      = "Azure KV Database Credentials"
       is_active = true
       azure_key_vault_value = {
         credential_provider_integration_id = "a1b2c3d4-e5f6-7890-abcd-ef1234567890"
         secret_name_1                      = "db-username"
         secret_name_2                      = "db-password"
         private_network_access             = false
       }
     }
     ```

4. Apply the Terraform configuration:

   ```shell
   terraform apply
   ```

Your Azure Key Vault Credential Provider is now ready to use in your Access Policies!

## Troubleshooting

[Section titled “Troubleshooting”](#troubleshooting)

* **UI/Integration Errors:** If you encounter errors when creating the integration or credential provider (for example UI logout, 500 errors), verify Azure permissions and configuration details.
* **Secret Not Found:** Ensure the secret name matches exactly and that the federated app has the correct permissions.
* **Access Denied:** Double-check RBAC or access policy assignments in Azure Key Vault.
