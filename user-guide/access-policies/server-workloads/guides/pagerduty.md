---
type: how-to
title: "PagerDuty"
description: "This page describes how to configure Aembit to work with the PagerDuty Server Workload."
resource: https://docs.aembit.io/user-guide/access-policies/server-workloads/guides/pagerduty/
interface: web-ui
tags: ["it-tooling", "guide", "server-workload", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# PagerDuty


[PagerDuty](https://www.pagerduty.com/) is a digital operations management platform that helps businesses improve their incident response process. It allows teams to centralize their monitoring tools and manage incidents in real-time, reducing downtime and improving service reliability.

Below you can find the Aembit configuration required to work with the PagerDuty service as a Server Workload using the PagerDuty API. Aembit supports multiple authentication/authorization methods for PagerDuty. This page describes scenarios where the Credential Provider is configured for PagerDuty via:

* [OAuth 2.0 Authorization Code (3LO)](pagerduty.md#oauth-20-authorization-code)
* [OAuth 2.0 Client Credentials](pagerduty.md#oauth-20-client-credentials)

> **Prerequisites**
>
> Before proceeding with the configuration, ensure you have the following:
>
> * PagerDuty tenant.
> * Registered app in the PagerDuty tenant. If you have not registered an app before, you can follow the steps outlined in the subsequent sections or refer to the [official PagerDuty Developer documentation](https://developer.pagerduty.com/docs/dd91fbd09a1a1-register-an-app) for more detailed instructions.

## OAuth 2.0 Authorization Code

### Server Workload Configuration

1. Create a new Server Workload.

* **Name** - Choose a user-friendly name.

2. Configure the service endpoint:

* **Host** - `api.pagerduty.com`
* **Application Protocol** - HTTP
* **Port** - 443 with TLS
* **Forward to Port** - 443 with TLS
* **Authentication method** - HTTP Authentication
* **Authentication scheme** - Bearer

### Credential Provider Configuration

1. Log in to your [PagerDuty account](https://identity.pagerduty.com/global/authn/authentication/PagerDutyGlobalLogin/enter_email).

2. Navigate to the top menu, select **Integrations**, and then click on **App Registration**.

![PagerDuty Dashboard Navigation](https://docs.aembit.io/_astro/pagerduty_dashboard_navigation.DPI2Y9Q7_ZuYOSV.webp)

3. Click the **New App** button in the top right corner of the page.

![PagerDuty New App](https://docs.aembit.io/_astro/pagerduty_new_app.DKj-45BA_dxPUS.webp)

4. Fill in the name and description fields, choose **OAuth 2.0**, and then click **Next** to proceed.

5. Select **Scoped OAuth** as the authorization method.

6. Switch to the Aembit UI to create a new Credential Provider, selecting the OAuth 2.0 Authorization Code credential type. After setting up the Credential Provider, copy the **Callback URL**.

7. Return to PagerDuty and click to **Add Redirect URL** and paste the copied **Callback URL** into the field.

8. Choose the permissions (scopes) for your application based on your needs.

9. Before registering your app, scroll down and click **Copy to clipboard** to store your selected permission scopes for later use in the tenant configuration.

![PagerDuty Copy Scopes](https://docs.aembit.io/_astro/pagerduty_copy_scopes.ByEo9t9f_1DIFM2.webp)

10. After making all of your selections, click on **Register App**.

11. A pop-up window appears. Copy both the Client ID and Client Secret, and store these details securely for later use in the tenant configuration.

![PagerDuty Copy Client ID and Secret](https://docs.aembit.io/_astro/pagerduty_copy_client_id_and_secret.ragxi8IF_ZDuA4O.webp)

12. Edit the existing Credential Provider created in the previous steps.

* **Name** - Choose a user-friendly name.
* **Credential Type** - [OAuth 2.0 Authorization Code](../../credential-providers/oauth-authorization-code.md)
* **Callback URL (Read-Only)** - Aembit auto-generates this read-only URL.
* **Client Id** - Provide the client ID copied from PagerDuty.
* **Client Secret** - Provide the client secret copied from PagerDuty.
* **Scopes** - Enter the scopes you use, space delimited. (e.g. `incidents.read abilities.read`).
* **OAuth URL** - `https://identity.pagerduty.com/global/oauth/anonymous/.well-known/openid-configuration`

Click on **URL Discovery** to populate the Authorization and Token URL fields. These fields need to be updated to the following values:

* **Authorization URL** - `https://identity.pagerduty.com/oauth/authorize`

* **Token URL** - `https://identity.pagerduty.com/oauth/token`

* **PKCE Required** - On

* **Lifetime** - 1 year (PagerDuty does not specify a refresh token lifetime; this value is recommended by Aembit.)

13. Click **Save** to save your changes on the Credential Provider.

14. In Aembit UI, click the **Authorize** button. You are then directed to a page where you can review the access request. Click **Accept** to complete the OAuth 2.0 Authorization Code flow. You should see a success page and be redirected to Aembit automatically. You can also verify that your flow is complete by checking the **State** value in the Credential Provider. After completion, it should be in a **Ready** state.

![Credential Provider - Ready State](https://docs.aembit.io/_astro/credential_providers_auth_code_status_ready.CBPCBiJg_ZBh135.webp)

> **Caution**
>
> Once the set lifetime ends, the retrieved credential expires and no longer be active. Aembit will notify you before this happens. Please ensure you reauthorize your credential before it expires.

## OAuth 2.0 Client Credentials

### Server Workload Configuration

1. Create a new Server Workload.

* **Name** - Choose a user-friendly name.

2. Configure the service endpoint:

* **Host** - `api.pagerduty.com`
* **Application Protocol** - HTTP
* **Port** - 443 with TLS
* **Forward to Port** - 443 with TLS
* **Authentication method** - HTTP Authentication
* **Authentication scheme** - Bearer

### Credential Provider Configuration

1. Log in to your [PagerDuty account](https://identity.pagerduty.com/global/authn/authentication/PagerDutyGlobalLogin/enter_email).

2. Navigate to the top menu, select **Integrations**, and then click on **App Registration**.

![PagerDuty Dashboard Navigation](https://docs.aembit.io/_astro/pagerduty_dashboard_navigation.DPI2Y9Q7_ZuYOSV.webp)

3. Click the **New App** button in the top right corner of the page.

![PagerDuty New App](https://docs.aembit.io/_astro/pagerduty_new_app.DKj-45BA_dxPUS.webp)

4. Fill in the name and description fields, choose **OAuth 2.0**, and then click **Next** to proceed.

5. Select **Scoped OAuth** as the authorization method and choose the permissions (scopes) for your application based on your needs.

6. Before registering your app, scroll down and click **Copy to clipboard** to store your selected permission scopes for later use in the tenant configuration.

![PagerDuty Copy Scopes](https://docs.aembit.io/_astro/pagerduty_copy_scopes.ByEo9t9f_1DIFM2.webp)

7. After making all of your selections, click on **Register App**.

8. A pop-up window appears. Copy both the Client ID and Client Secret, and store these details securely for later use in the tenant configuration.

![PagerDuty Copy Client ID and Secret](https://docs.aembit.io/_astro/pagerduty_copy_client_id_and_secret.ragxi8IF_ZDuA4O.webp)

9. Create a new Credential Provider.

* **Name** - Choose a user-friendly name.
* **Credential Type** - [OAuth 2.0 Client Credentials](../../credential-providers/oauth-client-credentials.md)
* **Token endpoint** - `https://identity.pagerduty.com/oauth/token`
* **Client ID** - Provide the client ID copied from PagerDuty.
* **Client Secret** - Provide the client secret copied from PagerDuty.
* **Scopes** - Enter the scopes you use, space delimited. Must include the `as_account-` scope that identifies the PagerDuty account, using the format `{REGION}.{SUBDOMAIN}` (e.g. `as_account-us.dev-aembit incidents.read abilities.read`). For more detailed information, you can refer to the [official PagerDuty Developer Documentation](https://developer.pagerduty.com/docs/e518101fde5f3-obtaining-an-app-o-auth-token).
* **Credential Style** - POST Body

## Client Workload Configuration

Aembit now handles the credentials required to access the Server Workload, eliminating the need for you to manage them directly. You can safely remove any previously used credentials from the Client Workload.

If you access the Server Workload through an SDK or library, it is possible that the SDK/library may still require credentials to be present for initialization purposes. In this scenario, you can provide placeholder credentials. Aembit will overwrite these placeholder credentials with the appropriate ones during the access process.

## Access Policy

* Create an Access Policy for a Client Workload to access the PagerDuty Server Workload. Assign the newly created Credential Provider to this Access Policy.

## Required Features

* You will need to configure the [TLS Decrypt](../../../deploy-install/advanced-options/tls-decrypt/configure-tls-decrypt.md) feature to work with the PagerDuty Server Workload.

## Related

**Compatible credential providers**

* [API Key](../../credential-providers/api-key.md)
