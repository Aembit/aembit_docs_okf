---
type: how-to
title: "Create an Azure Entra Federation Credential Provider Integration"
description: "How to create a Azure Entra Federation Credential Provider Integration using Azure Key Vault"
resource: https://docs.aembit.io/user-guide/access-policies/credential-providers/integrations/azure-entra-federation/
interface: web-ui
tags: ["integration", "credential-provider", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Create an Azure Entra Federation Credential Provider Integration

The Azure Entra Federation Credential Provider Integration allows you to create an [Azure Key Vault Credential Provider](../azure-key-vault.md). This enables the credential provider to retrieve secret values from Azure Key Vault without requiring long-lived secrets or static credentials. It leverages Azure’s Workload Identity Federation, allowing Aembit to authenticate using short-lived, federated tokens based on OpenID Connect (OIDC) standards. This page details everything you need to create an Azure Entra Federation Credential Provider Integration.

See [How the Azure Entra Federation integration works](overview.md#azure-entra-federation-integration) for more details.

> **Using multiple Azure Key Vaults**
>
> **For a single Key Vault instance**: Use one integration per Key Vault, regardless of the number of secrets you need to access in that Key Vault.
>
> **For multiple Key Vault instances**: Create a separate Azure Entra Federation integration and Credential Provider for each Key Vault.

## Prerequisites

To configure an Azure Entra Federation integration, you must have and do the following:

* Ability to access and manage your Aembit Tenant.

* Ability to access and manage a [Microsoft Entra registered application](https://learn.microsoft.com/en-us/entra/identity-platform/quickstart-register-app)

* An Azure Key Vault with secrets you want to manage via Aembit.

* Terraform only:

  * You have Terraform installed.

  * You have the [Aembit Terraform Provider](https://registry.terraform.io/providers/Aembit/aembit/latest) configured.

## Create an integration

This section explains how to configure an Azure Entra Federation integration in the Aembit web UI. These steps assume you already have a Microsoft Entra registered application (see [Prerequisites](#prerequisites)).

You must configure the Aembit integration at the same time as the Azure Entra registered application credential.

> **Tip**
>
> It’s best to have your Azure Entra registered application open in the Azure Entra Portal in a different browser window alongside the Aembit web UI while configuring the integration.

1. Log into your Aembit Tenant, and in the left sidebar menu, go to **Credential Providers → Integrations**.

   ![Credential Provider - Integrations tab](https://docs.aembit.io/_astro/cp-integrations-page.Q7suvjMH_Z1SCKgo.webp)

2. (Optional) In the top right corner, select the [Resource Set](../../../administration/resource-sets/overview.md) that you want this Credential Provider Integration to reside.

3. Click **+ New**, which displays the **Integration** pop out menu.

4. Select **Azure Entra Federation**, and enter a **Display Name** and optional **Description**.

   ![Start of Azure Entra Federation Integration form](https://docs.aembit.io/_astro/cp-integration-azure-entra-federation-start.BtTrDLcI_Z1NgdSc.webp)

   Before filling out these fields, you must add the credential for your Azure Entra registered application in the Azure Entra Portal first.

   Keep the Aembit web UI open while you work on the next section.

## Add a credential for your Azure Entra registered app

In the Azure Entra Portal, create a new credential for your registered application:

1. In your Azure Entra Portal, go to **App registrations** and select your registered application from the list.

2. Go to **Manage → Certificates & secrets** and select the **Federated Credentials** tab.

3. Click **Add credential**, to reveal the **Add a credential** page and fill out the following sections (for quick reference, see the [mappings](#azure-entra-and-integration-value-mappings) section):

4. For **Connect your account** -

   * **Federated credential scenario** - Select **Other issuer**

   * **Issuer** - From the Aembit Integration form, copy and paste the **OIDC Issuer URL**

   * **Type** - Select **Explicit subject identifier**

   * **Value** - Enter the desired value (this must match the **JWT Token Subject** value you enter on the Aembit Integration form)

5. For **Credential details** -

   * **Name** - Enter the desired name

   * **Audience** - Use the default value or optionally change it to the desired value (this must match the **Audience** value on the Aembit Integration form)

   Your Aembit Integration form and Entra registered application credential should look similar to the following example:

   ![Aembit web UI and Azure Entra registered app credential mappings](https://docs.aembit.io/_astro/azure-entra-registered-app-to-integration-credential-value-mappings.bGVbB1Pg_NAekN.webp)

6. Click **Add** and your new credential shows up on the **Federated credentials** tab in Azure Entra.

7. While still on your registered application, go to the **Overview** section.

   Keep the Azure Entra Portal open to use it in the next section.

## Complete the integration in the Aembit web UI

Go back to the Aembit web UI, and complete the **Integration** form:

1. Use the info from your Azure Entra registered application’s **Overview** page to complete the following fields for the Aembit Integration (for quick reference, see the [mappings](#azure-entra-and-integration-value-mappings) section):

   1. **Azure Tenant ID** - copy and paste the **Directory (tenant) ID**.

   2. **Azure Client ID** - copy and paste the **Application (client) ID**.

   ![Azure Entra registered application overview page](https://docs.aembit.io/_astro/azure-entra-registered-app-values.DICDG_jg_Z1y4cY3.webp)

2. For **Azure Key Vault Name**, enter the name of your Azure Key Vault.

3. (Optional) Enable **Fetch Secret Names** to load the secret names from the Azure Key Vault. When enabled, secret names automatically populate in a dropdown when setting up the Azure Key Vault Credential Provider, making it easier to select secrets.

4. Click **Save**.

   Your Azure Entra Federation integration now displays in your list of Credential Provider Integrations in the Aembit web UI.

You’re now ready to use your Azure Entra Federation integration to create an [Azure Key Vault Credential Provider](../azure-key-vault.md)!

## Configure Azure Entra Federation using the Aembit Terraform provider

To configure an Azure Entra Federation integration using the [Aembit Terraform Provider](https://registry.terraform.io/providers/Aembit/aembit/latest), follow the steps in this section.

> **OIDC Issuer URL**
>
> When using the Aembit Terraform Provider, you won’t have the OIDC Issuer URL the Azure credential requires until *after* you apply the Terraform configuration successfully.
>
> Make sure you leave the Azure Entra **Add a credential** page open until after you have successfully applied the Terraform configuration. Then copy the value for `oidc_issuer_url` from the applied Terraform configuration to the **Issuer** field in the **Add a credential** page.

1. Follow the steps to [Add a credential for your Azure Entra registered app](#add-a-credential-for-your-azure-entra-registered-app).

   Leaving the **Issuer** blank and stopping before you add the new credential.

   Keep this page open as you’ll need some values from it.

2. Create a new Terraform configuration file (such as `azure-entra-federation.tf`) with the following structure:

   ```hcl
   provider "aembit" {
   }


   resource "aembit_credential_provider_integration" "azure_entra_federation" {
     name = "<Desired Integration Name>"
     description = "<Optional Description>"
     azure_entra_federation = {
       audience           = "<Audience from Azure Federated Credentials creation page>"
       subject            = "<Subject from Azure Federated Credentials creation page>"
       azure_tenant       = "<Azure Tenant ID from Azure Entra Application>"
       client_id          = "<Azure Client ID from Azure Entra Application>"
       key_vault_name     = "<Name of your Azure Key Vault>"
       fetch_secret_names = true
     }
   }
   ```

   Example Terraform resource file for Azure Key Vault

   ```hcl
   provider "aembit" {
   }


   resource "aembit_credential_provider_integration" "azure_entra_federation" {
     name = "Azure Key Vault Integration"
     description = "Integration for accessing Azure Key Vault secrets"
     azure_entra_federation = {
       audience           = "api://AzureADTokenExchange"
       subject            = "aembit:integration:keyvault"
       azure_tenant       = "7f492ad1-25ec-4bfe-9c3a-84b517de8f2c"
       client_id          = "3d845691-7abc-4def-a123-456789abcdef"
       key_vault_name     = "my-key-vault"
       fetch_secret_names = true
     }
   }
   ```

3. Apply the Terraform configuration:

   ```shell
   terraform apply
   ```

4. After the Terraform apply completes successfully, the Aembit Terraform provider generates an OIDC Issuer URL as the value for `oidc_issuer_url`.

   Run the following command to obtain the value for `oidc_issuer_url`:

   ```shell
   terraform state show aembit_credential_provider_integration.azure_entra_federation
   ```

5. Copy the URL from `oidc_issuer_url` and return to the Azure Portal’s **Add a credential** page.

6. Paste the URL from `oidc_issuer_url` into the **Issuer** field.

7. Click **Add** and your new credential shows up on the **Federated credentials** tab in Azure Entra.

You’re now ready to use your Azure Entra Federation integration to create an [Azure Key Vault Credential Provider](../azure-key-vault.md)!

## Azure Entra and Integration value mappings

The following table shows how the different values in Azure Entra from your registered application map to the required values in the Aembit Integration and Terraform provider:

| Aembit Integration value | Azure Entra credential value | Azure UI location         | Terraform value      |
| ------------------------ | ---------------------------- | ------------------------- | -------------------- |
| OIDC Issuer URL          | Account Issuer               | Integration form          | Auto-populated       |
| Audience                 | Credential Audience          | Registered app credential | `audience`           |
| JWT Token Subject        | Account Value                | Registered app credential | `subject`            |
| Azure Tenant ID          | Directory (tenant) ID        | Your app’s Overview       | `azure_tenant`       |
| Azure Client ID          | Application (client) ID      | Your app’s Overview       | `client_id`          |
| Azure Key Vault Name     | Key Vault name               | Azure Key Vault resource  | `key_vault_name`     |
| Fetch Secret Names       | N/A                          | Integration form          | `fetch_secret_names` |

## Additional resources

* [Credential Provider Integrations overview](overview.md)
