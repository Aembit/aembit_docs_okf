---
type: how-to
title: "Configure an Azure Entra WIF Credential Provider"
description: "This page describes the Azure Entra Workload Identity Federation (WIF) Credential Provider and its usage with Server Workloads."
resource: https://docs.aembit.io/user-guide/access-policies/credential-providers/azure-entra-workload-identity-federation/
interface: web-ui
tags: [credential-provider, access-policy]
timestamp: 2026-06-26T15:17:49-07:00
type_inferred: true
---

# Configure an Azure Entra WIF Credential Provider

Aembit’s Credential Provider for Microsoft Azure Entra Workload Identity Federation (WIF) enables you to automatically obtain credentials through Aembit as a third-party federated Identity Provider (IdP). This allows you to securely authenticate with Azure Entra to access your Azure Entra registered applications and managed identities. For example, to assign API permissions or app roles to you registered applications or managed identities.

You can configure the Azure Entra Credential Provider using the [Aembit web UI](#configure-a-credential-provider-for-azure-entra) or through the [Aembit Terraform provider](#configure-azure-entra-using-the-aembit-terraform-provider).

## Prerequisites

[Section titled “Prerequisites”](#prerequisites)

To configure an Azure Entra Credential Provider, you must have and do the following:

* Ability to access and manage your Aembit Tenant.

* Ability to access and manage either of the following:

  * [Microsoft Entra registered application](https://learn.microsoft.com/en-us/entra/identity-platform/quickstart-register-app)

  * [Microsoft Managed Identity](https://learn.microsoft.com/en-us/entra/identity/managed-identities-azure-resources/overview)

* You request only one resource per Azure Entra Credential Provider

  See detailed example

  Azure’s architecture requires that you request only one resource per Azure Entra Credential Provider.

  For example, when you need to access both [Microsoft Graph](http://graph.microsoft.com/) and [Azure Management](http://management.azure.com/), you must configure the following:

  * **Two distinct Credential Providers** -

    * One requesting the `https://graph.microsoft.com/.default` scope

    * Another requesting the `https://management.azure.com/.default` scope

  * **Two distinct Server Workloads** -

    * One for `graph.microsoft.com`

    * Another for `management.azure.com`

  * **In your Access Policies, map each Credential Provider to its respective Server Workload**.

* Terraform only:

  * You have Terraform installed.

  * You have the [Aembit Terraform Provider](https://registry.terraform.io/providers/Aembit/aembit/latest) configured.

## Configure a Credential Provider for Azure Entra

[Section titled “Configure a Credential Provider for Azure Entra”](#configure-a-credential-provider-for-azure-entra)

This section explains how to configure an Azure Entra Credential Provider in the Aembit web UI that requests a single Azure Entra resource. These steps assume you already have a Microsoft Entra registered application (see [Prerequisites](#prerequisites)).

You must configure the Aembit Credential Provider at the same time as the Azure Entra registered application credential.

Tip

It’s best to have your Azure Entra registered application open in the Azure Entra Portal in a different browser window. Keep it open alongside the Aembit web UI while configuring the Credential Provider.

## Create a Credential Provider

[Section titled “Create a Credential Provider”](#create-a-credential-provider)

1. Log in to your Aembit Tenant, and in the left sidebar menu, go to **Credential Providers**.

2. Click **+ New**, which reveals the **Credential Provider** page.

3. Enter a **Name** and optional **Description**.

4. In the **Credential Type** dropdown, select **Azure Entra Identity Federation**, revealing new fields.

   ![Aembit web UI Credential Provider page](https://docs.aembit.io/_astro/azure-entra-aembit-credential-provider.B9S4K3CE_ZWz4Jo.webp)

   Before filling out these fields, you must add the credential for your Azure Entra registered application in the Azure Entra Portal first.

   Keep the Aembit web UI open while you work on the next section.

## Add a credential for your Azure Entra registered app

[Section titled “Add a credential for your Azure Entra registered app”](#add-a-credential-for-your-azure-entra-registered-app)

In the Azure Entra Portal, create a new credential for your registered application:

1. In your Azure Entra Portal, go to **App registrations** and select your registered application from the list.

2. Go to **Manage —> Certificates & secrets** and select the **Federated Credentials** tab.

3. Click **Add credential**, to reveal the **Add a credential** page and fill out the following sections (for quick reference, see the [mappings](#azure-entra-and-credential-provider-ui-value-mappings) section):

4. For **Connect your account** -

   * **Federated credential scenario** - Select **Other issuer**

   * **Issuer** - From the Aembit Credential Provider page, copy and paste the **OIDC Issuer URL**

   * **Type** - Select **Explicit subject identifier**

   * **Value** - Enter the desired value (this must match the **JWT Token Subject** value on the Aembit Credential Provider page)

5. For **Credential details** -

   * **Name** - Enter the desired name

   * **Audience** - Use the default value or optionally change it to the desired value (this must match the **Audience** value on the Aembit Credential Provider page)

   Your Aembit Credential Provider UI and Entra registered application credential should look similar to the following example:

   ![Aembit web UI and Azure Entra registered app credential mappings](https://docs.aembit.io/_astro/azure-entra-registered-app-credential-value-mappings.OFKGbvNQ_ZYKJKb.webp)

6. Click **Add** and your new credential shows up on the **Federated credentials** tab in Azure Entra.

7. While still on your registered application, go to the **Overview** section.

   Keep the Azure Entra Portal open to use it in the next section.

## Complete the Credential Provider in the Aembit web UI

[Section titled “Complete the Credential Provider in the Aembit web UI”](#complete-the-credential-provider-in-the-aembit-web-ui)

Go back to the Aembit web UI, and complete the **Credential Provider** page:

1. For **JWT Token Scope**, enter the scope of the resource you want to request.

   For example, for Microsoft Graph, use `https://graph.microsoft.com/.default`.

2. Use the info from your Azure Entra registered application’s **Overview** page to complete the remaining fields for the Aembit Credential Provider (for quick reference, see the [mappings](#azure-entra-and-credential-provider-ui-value-mappings) section):

   1. **Azure Tenant ID** - copy and paste the **Directory (tenant) ID**.

   2. **Azure Client ID** - copy and paste the **Application (client) ID**.

   ![Azure Entra registered application overview page](https://docs.aembit.io/_astro/azure-entra-registered-app-values.DICDG_jg_Z1y4cY3.webp)

3. Click **Save**.

   Your Azure Entra Credential Provider now displays in your list of Credential Providers in the Aembit web UI.

## Verify the connection

[Section titled “Verify the connection”](#verify-the-connection)

To verify the connection between your Aembit Credential Provider and your Azure Entra registered application:

1. On the **Credential Providers** page, select the Credential Provider you just created.

2. Click **Verify**.

   After a few moments you should see a green banner display a “Verified Successfully” message.

   If you don’t receive a “Verified Successfully” message, go back through the values in your Credential Provider in the Aembit UI. Also check the credential in your Azure Entra registered application to make sure they’re correct.

You’re now ready to use your Credential Provider for Azure Entra Workload Identity Federation with your Server Workloads in an Aembit Access Policy!

## Configure Azure Entra using the Aembit Terraform provider

[Section titled “Configure Azure Entra using the Aembit Terraform provider”](#configure-azure-entra-using-the-aembit-terraform-provider)

To configure an Azure Entra Credential Provider using the [Aembit Terraform Provider](https://registry.terraform.io/providers/Aembit/aembit/latest), follow the steps in this section.

OIDC Issuer URL

When using the Aembit Terraform Provider, you won’t have the OIDC Issuer URL the Azure credential requires until *after* you apply the Terraform configuration successfully.

Make sure you leave the Azure Entra **Add a credential** page open until after you have successfully applied the Terraform configuration. Then copy the value for `oidc_issuer` from the applied Terraform configuration to the **Issuer** field in the **Add a credential** page.

1. Follow the steps to [Add a credential for your Azure Entra registered app](#add-a-credential-for-your-azure-entra-registered-app).

   Leaving the **Issuer** blank and stopping before you add the new credential.

   Keep this page open as you’ll need some values from it.

2. Create a new Terraform configuration file (such as `azure-wif.tf`) with the following structure:

   ```hcl
   provider "aembit" {
   }


   resource "aembit_credential_provider" "azureEntra" {
     name = "<Desired Credential Provider Name>"
     is_active = true
     azure_entra_workload_identity = {
       audience = "<Audience from Azure Federated Credentials creation page>"
       subject = "<Subject from Azure Federated Credentials creation page>"
       scope = "<Scope within Microsoft ecosystem for the Access Token>"
       azure_tenant = "<Azure Tenant ID from Azure Entra Application or Managed Identity>"
       client_id = "<Azure Client ID from Azure Entra Application or Managed Identity>"
     }
   }
   ```

   Example Terraform resource file for Microsoft Graph

   ```hcl
   provider "aembit" {
   }


   resource "aembit_credential_provider" "azureEntra" {
     name = "Azure Entra WIF"
     is_active = true
     azure_entra_workload_identity = {
       audience = "api://AzureADTokenExchange"
       subject = "aembit:federation:test"
       scope = "https://graph.microsoft.com/.default"
       azure_tenant = "7f492ad1-25ec-4bfe-9c3a-84b517de8f2c"
       client_id = "3d845691-7abc-4def-a123-456789abcdef"
     }
   }
   ```

3. Apply the Terraform configuration:

   ```shell
   terraform apply
   ```

4. After the Terraform apply completes successfully, the Aembit Terraform provider generates an OIDC Issuer URL as the value for `oidc_issuer`.

   Run the following command to obtain the value for `oidc_issuer`:

   ```shell
   terraform state show aembit_credential_provider.azureEntra
   ```

5. Copy the URL from `oidc_issuer` and return to the Azure Portal’s **Add a credential** page.

6. Paste the URL from `oidc_issuer` into the **Issuer** field.

7. Click **Add** and your new credential shows up on the **Federated credentials** tab in Azure Entra.

You’re now ready to use your Credential Provider for Azure Entra Workload Identity Federation with your Server Workloads in an Aembit Access Policy!

## Azure Entra and Credential Provider UI value mappings

[Section titled “Azure Entra and Credential Provider UI value mappings”](#azure-entra-and-credential-provider-ui-value-mappings)

The following table shows how the different value in Azure Entra from your registered application map to the required values to the Aembit Credential Provider web UI and Terraform provider:

| Aembit Credential Provider value | Azure Entra credential value | Azure UI location         | Terraform value |
| -------------------------------- | ---------------------------- | ------------------------- | --------------- |
| OIDC Issuer URL                  | Account Issuer               | Registered app credential | Auto-populated  |
| Audience                         | Credential Audience          | Registered app credential | `audience`      |
| JWT Token Subject                | Account Value                | Registered app credential | `subject`       |
| Azure Tenant ID                  | Directory (tenant) ID        | Your app’s Overview       | `azure_tenant`  |
| Azure Client ID                  | Application (client) ID      | Your app’s Overview       | `client_id`     |
