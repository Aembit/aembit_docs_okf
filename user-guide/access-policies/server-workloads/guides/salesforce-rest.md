---
type: how-to
title: "Salesforce REST"
description: "How to configure Aembit to work with the Salesforce REST Server Workload"
resource: https://docs.aembit.io/user-guide/access-policies/server-workloads/guides/salesforce-rest/
interface: web-ui
tags: ["crm", "guide", "server-workload", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Salesforce REST

[Salesforce](https://www.salesforce.com/) is a cloud-based platform that helps businesses manage customer relationships, sales, and services. It supports integration with tools and offers customization to fit different business needs.

You can find the Aembit configuration required to work with the Salesforce service as a Server Workload using the Salesforce apps and APIs.

Aembit supports multiple authentication and authorization methods for Salesforce. This page describes scenarios where you configure the Credential Provider for Salesforce via:

* [OAuth 2.0 Authorization Code (3LO)](#oauth-20-authorization-code)
* [OAuth 2.0 Client Credentials](#oauth-20-client-credentials)

> **Prerequisites**
>
> Before you begin, ensure you have a Salesforce account and a connected app on Salesforce. If you haven’t set up an app yet, follow the steps under the Salesforce App Configuration section in the flow you use.
>
> For detailed information on Salesforce Apps, see the [official Salesforce documentation](https://help.salesforce.com/s/articleView?id=xcloud.external_integrations.htm\&type=5).

## OAuth 2.0 authorization code

### Server Workload configuration

To retrieve connection information in Salesforce:

1. In the upper-right corner of any page, click your profile photo. The endpoint appears in the dropdown menu under your username. Copy the endpoint.

   ![Salesforce endpoint](https://docs.aembit.io/_astro/salesforce_domain.DzvMfNsq_6QUcs.webp)

2. Create a new Server Workload.

   * **Name** - Choose a user-friendly name.

3. Configure the service endpoint:

   * **Host** - `<domain>.my.salesforce.com` (Provide the endpoint copied from Salesforce)
   * **Application Protocol** - HTTP
   * **Port** - 443
   * **Forward to Port** - 443 with TLS
   * **Authentication method** - HTTP Authentication
   * **Authentication scheme** - Bearer

### Salesforce app configuration

1. Log in to your [Salesforce account](https://login.salesforce.com/).

2. In the upper-right corner of any page, click the cog icon and then click **Setup**.

   ![Salesforce Setup](https://docs.aembit.io/_astro/salesforce_dashboard_to_setup.PSFnW-hZ_Z1GQTB9.webp)

3. In the search box at the top of the Setup page, type **App Manager** and select it from the search results.

4. In the top-right corner of the page, click **New External Client App**.

   ![New External App](https://docs.aembit.io/_astro/salesforce_new_connected_app.BBeTZroI_XX8gy.webp)

5. Configure the app based on your preferences. Below are key choices:

   * Provide a name for your connected app. The API Name auto-generates based on the app name, but you can edit it if needed.
   * Enter a valid email address in the **Contact Email** field.
   * Scroll down and expand the **API (Enable OAuth Settings)** section.
   * Check the **Enable OAuth** box.
   * Switch to the Aembit UI to create a new Credential Provider, selecting the **OAuth 2.0 Authorization Code** credential type.
   * After setting up the Credential Provider, copy the **Callback URL**.
   * Return to Salesforce and paste the copied URL into the **Callback URL** field.
   * Select the necessary **OAuth Scopes** for your application based on your needs.
   * Under the **Security** section, check the **Require secret for Web Server Flow** box.
   * Check the **Require secret for Refresh Token Flow** box.
   * Check the **Require Proof Key for Code Exchange (PKCE) Extension for Supported Authorization Flows** box.
   * At the bottom of the page, click **Create** to complete the app creation process.

   ![Configure External App 3LO flow](https://docs.aembit.io/_astro/salesforce_configure_external_app_3lo.COo_gKfM_CY5JL.webp)

For detailed information on the OAuth 2.0 Web Server Flow on Salesforce, see the [official Salesforce documentation](https://help.salesforce.com/s/articleView?id=xcloud.remoteaccess_oauth_web_server_flow.htm\&type=5).

### Credential Provider configuration

> **Note**
>
> If you are continuing from the previous section, click the **Settings** tab on the opened page and you can skip to step 6.

1. Log in to your [Salesforce account](https://login.salesforce.com/).

2. In the upper-right corner of any page, click the cog icon and then click **Setup**.

   ![Salesforce Setup](https://docs.aembit.io/_astro/salesforce_dashboard_to_setup.PSFnW-hZ_Z1GQTB9.webp)

3. On the left-side menu, scroll down and find **External Client Apps** under Platform Tools.

4. Expand it and click **External Client App Manager** under it.

5. Find your app from the list and click the icon at the end of the row. Select **Edit Settings** from the dropdown menu.

   ![External App List](https://docs.aembit.io/_astro/salesforce_view_external_app_from_list.CkuJMYB-_1DmClR.webp)

6. Scroll down and expand the **OAuth Settings** section.

7. Click the **Consumer Key and Secret**. Salesforce asks you to verify your identity.

   ![Consumer Details](https://docs.aembit.io/_astro/salesforce_external_app_details_to_consume_keys.D1T76C-4_1BRCvl.webp)

8. After verifying your identity, on the opened page, copy both the **Consumer Key** and **Consumer Secret**. Store these details securely for later use in the tenant configuration.

   ![Copy Consumer Key and Secret](https://docs.aembit.io/_astro/salesforce_external_app_consumer_key_and_secret.FOAt1kz2_1HgY93.webp)

9. Create a new Credential Provider.

   * **Name** - Choose a user-friendly name.
   * **Credential Type** - [OAuth 2.0 Authorization Code](../../credential-providers/oauth-authorization-code.md)
   * **Callback URL (Read-Only)** - Aembit auto-generates this read-only URL.
   * **Client ID** - Provide the Consumer Key copied from Salesforce.
   * **Client Secret** - Provide the Consumer Secret copied from Salesforce.
   * **Scopes** - You can leave this field empty, as Salesforce defaults to your selected scopes for the app.
   * **OAuth URL** - `https://<domain>.my.salesforce.com/`

   Click **URL Discovery** to populate the Authorization and Token URL fields, which you can leave as populated.

   * **PKCE Required** - On
   * **Lifetime** - 1 year (Salesforce doesn’t specify a refresh token lifetime. Aembit recommends this value.)

10. Click **Save** to save your changes on the Credential Provider.

11. In the Aembit UI, click **Authorize**. Aembit directs you to a page where you can review the access request. Click **Accept** to complete the OAuth 2.0 Authorization Code flow. You should see a success page and Aembit redirects you automatically. You can also verify that your flow is complete by checking the **State** value in the Credential Provider. After completion, it should be in a **Ready** state.

![Credential Provider - Ready State](https://docs.aembit.io/_astro/credential_providers_auth_code_status_ready.CBPCBiJg_ZBh135.webp)

> **Caution**
>
> Once the set lifetime ends, the retrieved credential expires and is no longer active. Aembit notifies you before this happens. Ensure you reauthorize your credential before it expires.

## OAuth 2.0 client credentials

### Server Workload configuration

To retrieve connection information in Salesforce:

1. In the upper-right corner of any page, click your profile photo. The endpoint appears in the dropdown menu under your username. Copy the endpoint.

   ![Salesforce endpoint](https://docs.aembit.io/_astro/salesforce_domain.DzvMfNsq_6QUcs.webp)

2. Create a new Server Workload.

   * **Name** - Choose a user-friendly name.

3. Configure the service endpoint:

   * **Host** - `<domain>.my.salesforce.com` (Provide the endpoint copied from Salesforce)
   * **Application Protocol** - HTTP
   * **Port** - 443
   * **Forward to Port** - 443 with TLS
   * **Authentication method** - HTTP Authentication
   * **Authentication scheme** - Bearer

### Salesforce app configuration

1. Log in to your [Salesforce account](https://login.salesforce.com/).

2. In the upper-right corner of any page, click the cog icon and then click **Setup**.

   ![Salesforce Setup](https://docs.aembit.io/_astro/salesforce_dashboard_to_setup.PSFnW-hZ_Z1GQTB9.webp)

3. In the search box at the top of the Setup page, type **App Manager** and select it from the search results.

4. In the top-right corner of the page, click **New External Client App**.

   ![New External App](https://docs.aembit.io/_astro/salesforce_new_connected_app.BBeTZroI_XX8gy.webp)

5. Configure the app based on your preferences. Below are key choices:

   * Provide a name for your connected app. The API Name auto-generates based on the app name, but you can edit it if needed.
   * Enter a valid email address in the **Contact Email** field.
   * Scroll down and expand the **API (Enable OAuth Settings)** section.
   * Check the **Enable OAuth** box.
   * Enter a placeholder URL such as `https://aembit.io` in the Callback URL field to pass the required check. (This field isn’t used for the Client Credentials Flow.)
   * Select the necessary **OAuth Scopes** for your application based on your needs.
   * Check the **Enable Client Credentials Flow** box. When the pop-up window appears, click **OK** to proceed.
   * Clear the **Proof Key for Code Exchange**, **Require Secret for Web Server Flow**, and **Require Secret for Refresh Token Flow** boxes.
   * At the bottom of the page, click **Create** to complete the app creation process.

   ![Configure External App CC flow](https://docs.aembit.io/_astro/salesforce_configure_external_app_cc.68XCKWoF_29sQPg.webp)

   > **Note**
   >
   > Salesforce requires you to designate an execution user, allowing the platform to generate access tokens for the chosen user.

6. On the detail page of your newly created app, click **Edit**.

7. Expand the **OAuth Policies** section.

8. Under the **OAuth Flows and External Client App Enhancements** section, check **Enable Client Credentials Flow**.

9. Enter the email address of the user you want to designate into the **Run As** field.

   ![Assign User to App](https://docs.aembit.io/_astro/salesforce_assign_user_to_app.CKQJ77Tz_2aT89D.webp)

For detailed information on the OAuth 2.0 Client Credentials Flow on Salesforce, see the [official Salesforce documentation](https://help.salesforce.com/s/articleView?id=sf.remoteaccess_oauth_client_credentials_flow.htm\&type=5).

### Credential Provider configuration

> **Note**
>
> If you are continuing from the previous section, click the **Settings** tab on the opened page and you can skip to step 6.

1. Log in to your [Salesforce account](https://login.salesforce.com/).

2. In the upper-right corner of any page, click the cog icon and then click **Setup**.

   ![Salesforce Setup](https://docs.aembit.io/_astro/salesforce_dashboard_to_setup.PSFnW-hZ_Z1GQTB9.webp)

3. On the left-side menu, scroll down and find **External Client Apps** under Platform Tools.

4. Expand it and click **External Client App Manager** under it.

5. Find your app from the list and click the icon at the end of the row. Select **Edit Settings** from the dropdown menu.

   ![External App List](https://docs.aembit.io/_astro/salesforce_view_external_app_from_list.CkuJMYB-_1DmClR.webp)

6. Scroll down and expand the **OAuth Settings** section.

7. Click the **Consumer Key and Secret**. Salesforce asks you to verify your identity.

   ![Consumer Details](https://docs.aembit.io/_astro/salesforce_external_app_details_to_consume_keys.D1T76C-4_1BRCvl.webp)

8. After verifying your identity, on the opened page, copy both the **Consumer Key** and **Consumer Secret**. Store these details securely for later use in the tenant configuration.

   ![Copy Consumer Key and Secret](https://docs.aembit.io/_astro/salesforce_external_app_consumer_key_and_secret.FOAt1kz2_1HgY93.webp)

9. Create a new Credential Provider.

   * **Name** - Choose a user-friendly name.
   * **Credential Type** - [OAuth 2.0 Client Credentials](../../credential-providers/oauth-client-credentials.md)
   * **Token endpoint** - `https://<domain>.my.salesforce.com/services/oauth2/token`
   * **Client ID** - Provide the Consumer Key copied from Salesforce.
   * **Client Secret** - Provide the Consumer Secret copied from Salesforce.
   * **Scopes** - You can leave this field empty, as Salesforce defaults to your selected scopes for the app.
   * **Credential Style** - Authorization Header

## Client workload configuration

Aembit now handles the credentials required to access the Server Workload, eliminating the need for you to manage them directly. You can remove any previously used credentials from the Client Workload.

If you access the Server Workload through SDK or library, the SDK or library may still require credentials to be present for initialization purposes. In this scenario, you can provide placeholder credentials. Aembit overwrites these placeholder credentials with the appropriate ones during the access process.

## Access Policy

Create an Access Policy for a Client Workload to access the Salesforce Server Workload. Assign the newly created Credential Provider to this Access Policy.

## Related

**Compatible credential providers**

* [OAuth 2.0 Client Credentials](../../credential-providers/oauth-client-credentials.md)
