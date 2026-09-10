---
type: how-to
title: "Google Drive"
description: "This page describes how to configure Aembit to work with the Google Drive Server Workload."
resource: https://docs.aembit.io/user-guide/access-policies/server-workloads/guides/google-drive/
interface: web-ui
tags: ["productivity", "guide", "server-workload", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Google Drive


[Google Drive](https://www.google.com/drive/), part of Google Workspace, is a cloud-based storage solution designed for storing, sharing, and collaborating on files.

Below you can find the Aembit configuration required to work with the Google Drive service as a Server Workload using the Google Drive API.

> **Prerequisites**
>
> Before proceeding with the configuration, ensure you have the following:
>
> * An active Google Cloud account
> * A GCP project with Google Drive enabled

### Server Workload Configuration

1. Create a new Server Workload.

* **Name** - Choose a user-friendly name.

2. Configure the Service endpoint:

* **Host** - `www.googleapis.com`
* **Application Protocol** - HTTP
* **Port** - 443 with TLS
* **Forward to Port** - 443 with TLS
* **Authentication method** - HTTP Authentication
* **Authentication scheme** - Bearer

### Credential Provider Configuration

1. Sign in to the Google Cloud Console and navigate to the [Credentials](hhttps://console.cloud.google.com/apis/credentials) page. Ensure you are working within a GCP project for which you have authorization.

2. On the **Credentials** dashboard, click **Create Credentials** located in the top left corner and select the **OAuth client ID** option.

![Create OAuth client ID](https://docs.aembit.io/_astro/gcp_create_oauth_client_id.Bslva-4Y_2chrWM.webp)

3. If there is no configured Consent Screen for your project, you see a **Configure Consent Screen** button on the directed page. Click the button to continue.

![Configure Consent Screen](https://docs.aembit.io/_astro/gcp_no_consent_screen.ByBGUKd3_2mifI7.webp)

4. Choose **User Type** and click **Create**.

   * Provide a name for your app.
   * Choose a user support email from the dropdown menu.
   * App logo and app domain fields are optional.
   * Enter at least one email for the Developer contact information field.
   * Click **Save and Continue**.
   * You may skip the Scopes step by clicking **Save and Continue** once again.
   * In the **Summary** step, review the details of your app and click **Back to Dashboard**.

5. Navigate back to [Credentials](hhttps://console.cloud.google.com/apis/credentials) page, click **Create Credentials**, and select the **OAuth client ID** option again.

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
* **Scopes** - Enter the scopes you will use for Google Drive. (e.g. `https://www.googleapis.com/auth/drive`) A full list of GCP Scopes can be found at [OAuth 2.0 Scopes for Google APIs](https://developers.google.com/identity/protocols/oauth2/scopes#drive).
* **OAuth URL** - `https://accounts.google.com`

Click on **URL Discovery** to populate the Authorization and Token URL fields, which can be left as populated.

* **PKCE Required** - Off
* **Lifetime** - 1 year (This value is recommended by Aembit. For more information, please refer to the [official Google documentation](https://developers.google.com/identity/protocols/oauth2#expiration).)

8. Click **Save** to save your changes on the Credential Provider.

9. In Aembit UI, click the **Authorize** button. You are directed to a page where you can choose your Google account first. Then click **Allow** to complete the OAuth 2.0 Authorization Code flow. You should see a success page and be redirected to Aembit automatically. You can also verify your flow is complete by checking the **State** value in the Credential Provider. After completion, it should be in a **Ready** state.

![Credential Provider - Ready State](https://docs.aembit.io/_astro/credential_providers_auth_code_status_ready.CBPCBiJg_ZBh135.webp)

> **Caution**
>
> Once the set lifetime ends, the retrieved credential expires and no longer be active. Aembit notifies you before this happens. Please ensure you reauthorize your credential before it expires.

## Client Workload Configuration

Aembit now handles the credentials required to access the Server Workload, eliminating the need for you to manage them directly. You can safely remove any previously used credentials from the Client Workload.

If you access the Server Workload through an SDK or library, it is possible that the SDK/library may still require credentials to be present for initialization purposes. In this scenario, you can provide placeholder credentials. Aembit will overwrite these placeholder credentials with the appropriate ones during the access process.

## Access Policy

* Create an Access Policy for a Client Workload to access the Google Drive Server Workload. Assign the newly created Credential Provider to this Access Policy.

## Required Features

* You will need to configure the [TLS Decrypt](../../../deploy-install/advanced-options/tls-decrypt/configure-tls-decrypt.md) feature to work with the Google Drive Server Workload.
