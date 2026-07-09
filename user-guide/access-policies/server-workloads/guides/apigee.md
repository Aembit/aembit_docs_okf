---
type: how-to
title: "Apigee"
description: "This page describes how to configure Aembit to work with the Apigee Server Workload."
resource: https://docs.aembit.io/user-guide/access-policies/server-workloads/guides/apigee/
interface: web-ui
tags: [cloud, guide, server-workload, access-policy]
timestamp: 2026-07-07T20:36:18-07:00
type_inferred: true
---

# Apigee

[Google Apigee](https://cloud.google.com/apigee?hl=en) is a full lifecycle API management platform that enables organizations to design, secure, deploy, monitor, and scale APIs. With its comprehensive set of features and scalable architecture, Google Apigee empowers developers to build efficient, reliable, and secure APIs that drive business growth.

Below you can find the Aembit configuration required to work with the Google Apigee service as a Server Workload using the REST APIs.

Aembit supports multiple authentication/authorization methods for Apigee. This page describes how to configure the Credential Provider for Apigee via:

* [OAuth 2.0 Authorization Code (3LO)](apigee.md#oauth-20-authorization-code)
* [API Key](apigee.md#api-key)

Prerequisites

Before proceeding with the configuration, ensure you have the following:

* An active Google Cloud account
* An existing API Proxy (API Key Method)
* App set up in the Google Apigee platform

If you haven’t created a proxy before, you can follow the steps in the next section. For more information on creating an API Proxy, please refer to the [official Google documentation](https://cloud.google.com/apigee/docs/api-platform/get-started/get-started).

## OAuth 2.0 authorization code

[Section titled “OAuth 2.0 authorization code”](#oauth-20-authorization-code)

### Server Workload configuration

[Section titled “Server Workload configuration”](#server-workload-configuration)

1. Create a new Server Workload.

* **Name** - Choose a user-friendly name.

2. Configure the Service endpoint:

* **Host** - `apigee.googleapis.com`
* **Application Protocol** - HTTP
* **Port** - 443 with TLS
* **Forward to Port** - 443 with TLS
* **Authentication method** - HTTP Authentication
* **Authentication scheme** - Bearer

### Credential Provider configuration

[Section titled “Credential Provider configuration”](#credential-provider-configuration)

1. Sign in to the Google Cloud Console and navigate to the [Credentials](https://console.cloud.google.com/apis/credentials) page. Ensure you are working within a GCP project for which you have authorization.

2. On the **Credentials** dashboard, click **Create Credentials** located in the top left corner and select the **OAuth client ID** option.

![Create OAuth client ID](https://docs.aembit.io/_astro/gcp_create_oauth_client_id.Bslva-4Y_2chrWM.webp)

3. If there is no configured Consent Screen for your project, you see **Configure Consent Screen** on the directed page. Click it to continue.

![Configure Consent Screen](https://docs.aembit.io/_astro/gcp_no_consent_screen.ByBGUKd3_2mifI7.webp)

4. Choose **User Type** and click **Create**.

   * Provide a name for your app.
   * Choose a user support email from the dropdown menu.
   * App logo and app domain fields are optional.
   * Enter at least one email for the Developer contact information field.
   * Click **Save and Continue**.
   * You may skip the Scopes step by clicking **Save and Continue** once again.
   * In the **Summary** step, review the details of your app and click **Back to Dashboard**.

5. Navigate back to [Credentials](https://console.cloud.google.com/apis/credentials) page, click **Create Credentials**, and select the **OAuth client ID** option again.

   * Choose **Web Application** for Application Type.
   * Provide a name for your web client.
   * Switch to the Aembit UI to create a new Credential Provider, selecting the OAuth 2.0 Authorization Code credential type. After setting up the Credential Provider, copy the **Callback URL**.
   * Return to Google Cloud Console and paste the copied URL into the **Authorized redirect URIs** field.
   * Click **Create**.

6. A pop-up window appears. Copy both the **Client ID** and the **Client Secret**. Store them for later use in the tenant configuration.

7. Edit the existing Credential Provider created in the previous steps.

* **Name** - Choose a user-friendly name.
* **Credential Type** - [OAuth 2.0 Authorization Code](../../credential-providers/oauth-authorization-code.md)
* **Callback URL (Read-Only)** - Aembit auto-generates this read-only URL.
* **Client Id** - Provide the Client ID copied from Google.
* **Client Secret** - Provide the Secret copied from Google.
* **Scopes** - Enter the scopes you use for Apigee (for example, `https://www.googleapis.com/auth/cloud-platform`) You can find a full list of GCP Scopes at [OAuth 2.0 Scopes for Google APIs](https://developers.google.com/identity/protocols/oauth2/scopes).
* **OAuth URL** - `https://accounts.google.com`

Click on **URL Discovery** to populate the Authorization and Token URL fields, which you can leave as populated.

* **PKCE Required** - Off
* **Lifetime** - 1 year (A Google Cloud Platform project with an OAuth consent screen configured for an external user type and a publishing status of Testing receives a refresh token expiring in 7 days).\
  Google doesn’t specify a refresh token lifetime for the internal user type selected version; Aembit recommends this value. For more information, refer to the [official Google documentation](https://developers.google.com/identity/protocols/oauth2#expiration).

8. Click **Save** to save your changes on the Credential Provider.

9. In Aembit UI, click **Authorize**. A page opens where you choose your Google Account first. Then click **Allow** to complete the OAuth 2.0 Authorization Code flow. A success page appears, and Aembit redirects you automatically. You can also verify your flow is complete by checking the **State** value in the Credential Provider. After completion, it should be in a **Ready** state.

![Credential Provider - Ready State](https://docs.aembit.io/_astro/credential_providers_auth_code_status_ready.CBPCBiJg_ZBh135.webp)

Caution

Once the set lifetime ends, the retrieved credential expires and is no longer active. Aembit notifies you before this happens. Please ensure you reauthorize your credential before it expires.

## API key

[Section titled “API key”](#api-key)

### Create an API proxy

[Section titled “Create an API proxy”](#create-an-api-proxy)

Note

The following steps outline a basic configuration for creating an Apigee API proxy. Keep in mind that Apigee supports additional customizations not detailed in these instructions.

1. Navigate to the [Apigee UI in Cloud console](https://console.cloud.google.com/apigee) and sign in with your Google Cloud account.

2. In the left sidebar, select **API Proxies** under the Proxy development section.

3. On the **API Proxies** dashboard, click **Create** in the top left corner.

![Create API Proxy](https://docs.aembit.io/_astro/apigee_create_api_proxy.Byo7U2xh_70NYV.webp)

4. When prompted, choose a proxy type. Keep the default **Reverse proxy** option and provide any other required information.

5. Once you have configured your proxy, deploy it to make the API proxy active.

### Server Workload configuration

[Section titled “Server Workload configuration”](#server-workload-configuration-1)

To locate the environment group hostname for your proxy in the Apigee UI, follow these steps:

* Navigate to the [Apigee UI](https://apigee.google.com/) and sign in with your Google Cloud account.

* In the Apigee UI, go to **Management > Environments > Groups**.

* Identify the row displaying the environment where you deployed your proxy.

* Copy the endpoint for later use in the tenant configuration.

1. Create a new Server Workload.

* **Name** - Choose a user-friendly name.

2. Configure the service endpoint:

* **Host** - `<environment-group-hostname>.com` (Provide the endpoint copied from Apigee UI)
* **Application Protocol** - HTTP
* **Port** - 443 with TLS
* **Forward to Port** - 443 with TLS
* **Authentication method** - API Key
* **Authentication scheme** - Query Parameter
* **Query Parameter** - apikey

### Credential Provider configuration

[Section titled “Credential Provider configuration”](#credential-provider-configuration-1)

1. Navigate to the [Apigee UI in Cloud console](https://console.cloud.google.com/apigee) and sign in with your Google Cloud account.

2. In the left sidebar, select **Apps** to access a list of your applications.

3. Click on the name of the app to view its details.

4. Within the **Credentials** section, click the icon to **Copy to clipboard** next to **Key** and securely store the key for later use in the tenant configuration.

![Copy Apigee API Key](https://docs.aembit.io/_astro/apigee_api_key.Dsc-52Lx_Z1pcvAC.webp)

5. Create a new Credential Provider.

* **Name** - Choose a user-friendly name.
* **Credential Type** - [API Key](../../credential-providers/api-key.md)
* **API Key** - Provide the key copied from Google Cloud Apigee console.

## Client Workload configuration

[Section titled “Client Workload configuration”](#client-workload-configuration)

Aembit now handles the credentials required to access the Server Workload, eliminating the need for you to manage them directly. You can remove any previously used credentials from the Client Workload.

If you access the Server Workload through an SDK or library, it’s possible that the SDK/library may still require credentials to be present for initialization purposes. In this scenario, you can provide placeholder credentials. Aembit overwrites these placeholder credentials with the appropriate ones during the access process.

## Access Policy

[Section titled “Access Policy”](#access-policy)

* Create an Access Policy for a Client Workload to access the Apigee Server Workload. Assign the newly created Credential Provider to this Access Policy.

## Required features

[Section titled “Required features”](#required-features)

* You need to configure the [TLS Decrypt](../../../deploy-install/advanced-options/tls-decrypt/configure-tls-decrypt.md) feature to work with the Apigee Server Workload.

## Related

**Compatible credential providers**

* [OAuth 2.0 Client Credentials](../../credential-providers/oauth-client-credentials.md)
