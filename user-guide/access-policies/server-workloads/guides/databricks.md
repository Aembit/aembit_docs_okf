---
type: how-to
title: "Databricks"
description: "This page describes how to configure Aembit to work with the Databricks Server Workload."
resource: https://docs.aembit.io/user-guide/access-policies/server-workloads/guides/databricks/
interface: web-ui
tags: [data-analytics, guide, server-workload, access-policy]
timestamp: 2026-04-27T11:40:47-04:00
type_inferred: true
---

# Databricks


[Databricks](https://www.databricks.com/) is a unified data analytics platform built on Apache Spark, designed for scalable big data processing and machine learning. It provides tools for data engineering, data science, and analytics, enabling efficient handling of complex data workloads.

Below you can find the Aembit configuration required to work with the Databricks service as a Server Workload using the Databricks REST API. Aembit supports multiple authentication/authorization methods for Databricks. This page describes scenarios where the Credential Provider is configured for Databricks via:

* [OAuth 2.0 Authorization Code (3LO)](databricks.md#oauth-20-authorization-code)
* [OAuth 2.0 Client Credentials](databricks.md#oauth-20-client-credentials)
* [API Key](databricks.md#api-key)

Prerequisites

Before proceeding with the configuration, ensure you have the following:

* Databricks tenant.
* Workspace in the Databricks tenant. If you have not created a workspace before, you can follow the steps outlined in the subsequent sections or refer to the [official Databricks documentation](https://docs.databricks.com/en/getting-started/onboarding-account.html) for more detailed instructions.

## Create a Workspace in Databricks

[Section titled “Create a Workspace in Databricks”](#create-a-workspace-in-databricks)

Note

The following steps outline the process for creating a workspace in Databricks on AWS. If you are using Google Cloud Platform (GCP) or Microsoft Azure, you can find the corresponding steps by changing the platform option in the top right corner of the Databricks documentation.

1. Sign in to the [Databricks Console](https://accounts.cloud.databricks.com/) and navigate to the **Workspaces** page.

2. Click **Create workspace** located in the top right corner, select the **Quickstart** option, and then click **Next**.

![Databricks Create Workspace](https://docs.aembit.io/_astro/databricks_create_workspace.DC-EbrK4_1nPAEf.webp)

3. In the next step, provide a name for your workspace, choose the AWS region, and then click **Start Quickstart**. This redirects you to the AWS Console.

4. In the AWS Console, you may change the pre-generated stack name if desired. Scroll down, check the acknowledgment box, and then click **Create stack**. The stack creation process may take some time. Once the creation is successfully completed, you receive a confirmation email from Databricks. You can then switch back to the Databricks console. If you do not see your workspace in the list, please refresh the page.

5. Click on the name of the workspace to view details. In the URL field, copy the part after the prefix (e.g., `abc12345` in `https://abc12345.cloud.databricks.com`). This is your Databricks instance name, and is used in future steps.

6. Click **Open Workspace** located in the top right corner to proceed with the next steps in the workspace setup.

![Databricks Workspace Details](https://docs.aembit.io/_astro/databricks_workspace_details.BNx5tgID_Z1vdLWH.webp)

## OAuth 2.0 Authorization Code

[Section titled “OAuth 2.0 Authorization Code”](#oauth-20-authorization-code)

### Server Workload Configuration

[Section titled “Server Workload Configuration”](#server-workload-configuration)

1. Create a new Server Workload.

* **Name** - Choose a user-friendly name.

2. Configure the service endpoint:

* **Host** - `<databricks-instance>.cloud.databricks.com` (Use the Databricks instance name copied in step 5 of the workspace creation process)
* **Application Protocol** - HTTP
* **Port** - 443 with TLS
* **Forward to Port** - 443 with TLS
* **Authentication method** - HTTP Authentication
* **Authentication scheme** - Bearer

### Credential Provider Configuration

[Section titled “Credential Provider Configuration”](#credential-provider-configuration)

1. In your Databricks account console, select **Settings** from the left-hand menu.

2. Navigate to the **App Connections** section in the top menu.

3. Click the **Add Connection** button in the top right corner.

![Databricks Add Connection](https://docs.aembit.io/_astro/databricks_app_creation.D8pIzppw_eUAHS.webp)

4. Enter the **name** of your app.

5. Switch to the Aembit UI to create a new Credential Provider, selecting the OAuth 2.0 Authorization Code credential type. After setting up the Credential Provider, copy the **Callback URL**.

6. Return to Databricks and paste the copied **Callback URL** into the **Redirect URLs** field.

7. Select the scopes for your application based on your specific needs.

Note

To avoid potential issues, do **not** to set the **Access Token TTL** to less than 10 minutes.

8. Once all selections are made, click **Add**.

9. A pop-up window appears. Copy both the **Client ID** and **Client Secret**, and securely store these details for later use in your tenant configuration.

![Databricks App Client Id and Client Secret](https://docs.aembit.io/_astro/databricks_app_clientid_and_secret.BkTgYMTD_1GBq4H.webp)

10. Edit the existing Credential Provider created in the previous steps.

* **Name** - Choose a user-friendly name.

* **Credential Type** - [OAuth 2.0 Authorization Code](../../credential-providers/oauth-authorization-code.md)

* **Callback URL (Read-Only)** - Aembit auto-generates this read-only URL.

* **Client Id** - Provide the client ID copied from Databricks.

* **Client Secret** - Provide the client secret copied from Databricks.

* **Scopes** - `all-apis offline_access` or `sql offline_access`, depending on your scope selection in the Databricks UI. For more details on scopes and custom OAuth applications, please refer to the [official Databricks documentation](https://docs.databricks.com/en/integrations/enable-disable-oauth.html#enable-custom-app-ui).

* **OAuth URL** -

  * For a **workspace-level** OAuth URL, use: `https://<databricks-instance>/oidc` (Use the Databricks instance name copied in step 5 of the workspace creation process)
  * For an **account-level** OAuth URL, use: `https://accounts.cloud.databricks.com/oidc/accounts/<account-id>`
    * In your Databricks account, click on your username in the upper right corner, and in the dropdown menu,copy the part next to Account ID and use it in the previous link. ![Databricks Account ID](https://docs.aembit.io/_astro/databricks_account_id.DIt8ah4V_ZQqfKC.webp)

Tip

These two URLs correspond to different levels of OAuth authorization. The level determines the scope of the authorization code:

* **Account-Level** - Use this URL if you need to call both account-level and workspace-level REST APIs across all accounts and workspaces that your Databricks user account has access to.
* **Workspace-Level** - Use this URL if you only need to call REST APIs within a single workspace that your user account has access to.

For more detailed information about these two different levels, please refer to the [official Databricks documentation](https://docs.databricks.com/en/dev-tools/auth/oauth-u2m.html#step-2-generate-an-authorization-code).

Click on **URL Discovery** to populate the Authorization and Token URL fields, which can be left as populated.

* **PKCE Required** - On
* **Lifetime** - 1 year (Databricks does not specify a refresh token lifetime; this value is recommended by Aembit.)

11. Click **Save** to save your changes on the Credential Provider.

12. In the Aembit UI, click the **Authorize** button. You are directed to a page where you can review the access request. Click **Authorize** to complete the OAuth 2.0 Authorization Code flow. You should see a success page and then be redirected to Aembit automatically. You can also verify your flow is complete by checking the **State** value in the Credential Provider. After completion, it should be **Ready**.

![Credential Provider - Ready State](https://docs.aembit.io/_astro/credential_providers_auth_code_status_ready.CBPCBiJg_ZBh135.webp)

Caution

Once the set lifetime ends, the retrieved credential expires and will not work anymore. Aembit will notify you before this happens. Please ensure you reauthorize the credential before it expires.

## OAuth 2.0 Client Credentials

[Section titled “OAuth 2.0 Client Credentials”](#oauth-20-client-credentials)

### Server Workload Configuration

[Section titled “Server Workload Configuration”](#server-workload-configuration-1)

1. Create a new Server Workload.

* **Name** - Choose a user-friendly name.

2. Configure the service endpoint:

* **Host** - `<databricks-instance>.cloud.databricks.com` (Use the Databricks instance name copied in step 5 of the workspace creation process)
* **Application Protocol** - HTTP
* **Port** - 443 with TLS
* **Forward to Port** - 443 with TLS
* **Authentication method** - HTTP Authentication
* **Authentication scheme** - Bearer

### Credential Provider Configuration

[Section titled “Credential Provider Configuration”](#credential-provider-configuration-1)

1. In your Databricks workspace, click your username in the top right corner, and select **Settings** from the dropdown menu.

2. In the left-hand menu, navigate to **Identity and access**.

3. Next to **Service principals**, click **Manage**.

![Databricks Service principals](https://docs.aembit.io/_astro/databricks_service_principals.D9AAuV5M_Z1bIVhN.webp)

4. Click the **Add service principal** button.

5. If you do not already have a service principal, click **Add New**; otherwise, select the desired service principal from the list and click **Add**.

6. Click on the name of the service principal to view its details.

7. Navigate to the **Permissions** tab and click the **Grant access** button.

8. In the pop-up window, select the User, Group, or Service Principal and assign their role, then click **Save**.

9. Navigate to the **Secrets** tab and click the **Generate secret** button.

10. A pop-up window appears. Copy both the **Client ID** and **Client Secret**, and store these details securely for later use in the tenant configuration.

![Service principals Client ID and Client Secret](https://docs.aembit.io/_astro/databricks_service_principal_clientid_secret.try_JVnA_2q0LMi.webp)

11. Create a new Credential Provider.

* **Name** - Choose a user-friendly name.

* **Credential Type** - [OAuth 2.0 Client Credentials](../../credential-providers/oauth-client-credentials.md)

* **Token endpoint** -

  * For a **workspace-level** endpoint URL, use: `https://<databricks-instance>/oidc/v1/token` (Use the Databricks instance name copied in step 5 of the workspace creation process)
  * For an **account-level** endpoint URL, use: `https://accounts.cloud.databricks.com/oidc/accounts/<my-account-id>/v1/token`
    * In your Databricks account, click on your username in the upper right corner, and in the dropdown menu,copy the part next to Account ID and use it in the previous link. ![Databricks Account ID](https://docs.aembit.io/_astro/databricks_account_id.DIt8ah4V_ZQqfKC.webp)

Tip

These two URLs correspond to different levels of OAuth authorization. The level determines the scope of the authorization code:

* **Account-Level** - Use this URL if you need to call both account-level and workspace-level REST APIs across all accounts and workspaces that your Databricks user account has access to.
* **Workspace-Level** - Use this URL if you only need to call REST APIs within a single workspace that your user account has access to.

For more detailed information about these two different levels, please refer to the [official Databricks documentation](https://docs.databricks.com/en/dev-tools/auth/oauth-m2m.html#manually-generate-and-use-access-tokens-for-oauth-m2m-authentication).

* **Client ID** - Provide the client ID copied from Databricks.
* **Client Secret** - Provide the client secret copied from Databricks.
* **Scopes** - `all-apis`
* **Credential Style** - Authorization Header

## API Key

[Section titled “API Key”](#api-key)

### Server Workload Configuration

[Section titled “Server Workload Configuration”](#server-workload-configuration-2)

1. Create a new Server Workload.

* **Name** - Choose a user-friendly name.

2. Configure the service endpoint:

* **Host** - `<databricks-instance>.cloud.databricks.com` (Use the Databricks instance name copied in step 5 of the workspace creation process)
* **Application Protocol** - HTTP
* **Port** - 443 with TLS
* **Forward to Port** - 443 with TLS
* **Authentication method** - HTTP Authentication
* **Authentication scheme** - Bearer

### Credential Provider Configuration

[Section titled “Credential Provider Configuration”](#credential-provider-configuration-2)

1. In your Databricks workspace, click on your username in the top right corner, and select **Settings** from the dropdown menu.

![Databricks Workspace Navigate Settings](https://docs.aembit.io/_astro/databricks_workspace_navigate_settings.BIBiy6d6_Z20d2bo.webp)

2. In the left-hand menu, navigate to the **Developer** section.

3. Next to **Access tokens**, click **Manage**.

4. Click the **Generate new token** button.

5. Optionally, provide a comment and set a lifetime for your token, then click **Generate**.

6. Click **Copy to clipboard** and securely store the token for later use in the configuration on the tenant.

![Databricks API Key](https://docs.aembit.io/_astro/databricks_api_key.BqUXcSap_1IcFJM.webp)

7. Create a new Credential Provider.

* **Name** - Choose a user-friendly name.
* **Credential Type** - [API Key](../../credential-providers/api-key.md)
* **API Key** - Paste the token copied from Databricks.

## Client Workload Configuration

[Section titled “Client Workload Configuration”](#client-workload-configuration)

Aembit now handles the credentials required to access the Server Workload, eliminating the need for you to manage them directly. You can safely remove any previously used credentials from the Client Workload.

If you access the Server Workload through an SDK or library, it is possible that the SDK/library may still require credentials to be present for initialization purposes. In this scenario, you can provide placeholder credentials. Aembit will overwrite these placeholder credentials with the appropriate ones during the access process.

## Access Policy

[Section titled “Access Policy”](#access-policy)

* Create an Access Policy for a Client Workload to access the Databricks Server Workload. Assign the newly created Credential Provider to this Access Policy.

## Required Features

[Section titled “Required Features”](#required-features)

* You will need to configure the [TLS Decrypt](../../../deploy-install/advanced-options/tls-decrypt/configure-tls-decrypt.md) feature to work with the Databricks Server Workload.

## Related

**Compatible credential providers**

* [API Key](../../credential-providers/api-key.md)
* [OAuth 2.0 Client Credentials](../../credential-providers/oauth-client-credentials.md)
