---
type: how-to
title: "Slack"
description: "This page describes how to configure Aembit to work with the Slack Server Workload."
resource: https://docs.aembit.io/user-guide/access-policies/server-workloads/guides/slack/
interface: web-ui
tags: [productivity, guide, server-workload, access-policy]
timestamp: 2026-04-27T11:40:47-04:00
type_inferred: true
---

# Slack


[Slack](https://slack.com/) is a cloud-based collaboration platform designed to enhance communication and teamwork within organizations. Slack offers channels for structured discussions, direct messaging, and efficient file sharing. With support for diverse app integrations, Slack serves as a centralized hub for streamlined workflows and improved team collaboration.

Below you can find the Aembit configuration required to work with the Slack service as a Server Workload using the Slack apps and APIs.

Aembit supports multiple authentication/authorization methods for Slack. This page describes scenarios where the Credential Provider is configured for Slack via:

* [OAuth 2.0 Authorization Code (3LO)](slack.md#oauth-20-authorization-code)
* [API Key](slack.md#api-key)

Prerequisites

Before proceeding with the configuration, ensure you have a Slack workspace and a Slack App with the necessary scopes. If you have not set up a Slack App yet, follow the steps under the Credential Provider configuration in the flow you will use. For detailed information on Slack Apps, please refer to the [official Slack documentation](https://api.slack.com/start/apps).

## OAuth 2.0 Authorization Code

[Section titled “OAuth 2.0 Authorization Code”](#oauth-20-authorization-code)

### Server Workload Configuration

[Section titled “Server Workload Configuration”](#server-workload-configuration)

1. Create a new Server Workload.

* **Name** - Choose a user-friendly name.

2. Configure the service endpoint:

* **Host** - `slack.com`
* **Application Protocol** - HTTP
* **Port** - 443 with TLS
* **Forward to Port** - 443 with TLS
* **Authentication method** - HTTP Authentication
* **Authentication scheme** - Bearer

### Credential Provider Configuration

[Section titled “Credential Provider Configuration”](#credential-provider-configuration)

1. Sign in to your Slack account.

2. Navigate to the [Slack - Your Apps](https://api.slack.com/apps) page.

3. Click on **Create an App**.

![Create an Slack App](https://docs.aembit.io/_astro/slack_create_an_app.BI3mB2EL_fMWcD.webp)

4. In the dialog that appears, choose **From Scratch**. Enter an App Name and select a workspace to develop your app in.

5. Click **Create** to proceed.

6. After the app is created, navigate to your app’s main page. Scroll down to the **App Credentials** section, and copy both the **Client ID** and the **Client Secret**. Store them for later use in the tenant configuration.

7. Scroll up to the **Add features and functionality** section, and click **Permissions**.

8. Switch to the Aembit UI to create a new Credential Provider, selecting the OAuth 2.0 Authorization Code credential type. After setting up the Credential Provider, copy the **Callback URL**.

9. Return to Slack, under **Redirect URLs**, click **Add New Redirect URL**, paste in the URL, click **Add**, and then click **Save URLs**.

10. In the **Scopes** section, under the **Bot Token Scopes**, click **Add an OAuth Scope** to add the necessary scopes for your application.

11. Scroll up to the **Advanced token security via token rotation** section, and click **Opt In**.

![Add Bot Token Scopes](https://docs.aembit.io/_astro/slack_add_bot_token_scopes.BuSqtwMV_ZqYAOm.webp)

12. Edit the existing Credential Provider created in the previous steps.

* **Name** - Choose a user-friendly name.
* **Credential Type** - [OAuth 2.0 Authorization Code](../../credential-providers/oauth-authorization-code.md)
* **Callback URL (Read-Only)** - Aembit auto-generates this read-only URL.
* **Client Id** - Provide the Client ID copied from Slack.
* **Client Secret** - Provide the Secret copied from Slack.
* **Scopes** - Enter the scopes you use, space delimited. A full list of Slack Scopes can be found in the [official Slack documentation](https://api.slack.com/scopes?filter=granular_bot).
* **OAuth URL** - `https://slack.com`

Click on **URL Discovery** to populate the Authorization and Token URL fields. These fields will need to be updated to the following values:

* **Authorization URL** - `https://slack.com/oauth/v2/authorize`
* **Token URL** - `https://slack.com/api/oauth.v2.access`
* **PKCE Required** - Off (PKCE is not supported by Slack, so leave this field unchecked).
* **Lifetime** - 1 year (Slack does not specify a refresh token lifetime; this value is recommended by Aembit.)

13. Click **Save** to save your changes on the Credential Provider.

14. In Aembit UI, click the **Authorize** button. You will be directed to a page where you can review the access request. Click **Allow** to complete the OAuth 2.0 Authorization Code flow. You will see a success page and will be redirected to Aembit automatically. You can also verify your flow is complete by checking the **State** value in the Credential Provider. After completion, it should be in a **Ready** state.

![Credential Provider - Ready State](https://docs.aembit.io/_astro/credential_providers_auth_code_status_ready.CBPCBiJg_ZBh135.webp)

Caution

Once the set lifetime ends, the retrieved credential will expire and no longer be active. Aembit will notify you before this happens. Please ensure you reauthorize your credential before it expires.

## API Key

[Section titled “API Key”](#api-key)

### Server Workload Configuration

[Section titled “Server Workload Configuration”](#server-workload-configuration-1)

1. Create a new Server Workload.

* **Name** - Choose a user-friendly name.

2. Configure the service endpoint:

* **Host** - `slack.com`
* **Application Protocol** - HTTP
* **Port** - 443 with TLS
* **Forward to Port** - 443 with TLS
* **Authentication method** - HTTP Authentication
* **Authentication scheme** - Bearer

### Credential Provider Configuration

[Section titled “Credential Provider Configuration”](#credential-provider-configuration-1)

1. Sign in to your Slack account.

2. Navigate to the [Slack - Your Apps](https://api.slack.com/apps) page.

3. Click on **Create an App**.

![Create a Slack App](https://docs.aembit.io/_astro/slack_create_an_app.BI3mB2EL_fMWcD.webp)

4. In the dialog that appears, choose either **From Scratch** or **From App Manifest**.

5. Depending on your selection, enter an App Name and select a workspace to develop your app in.

6. Click **Create** to proceed.

7. After the app is created, navigate to your app’s main page. Select and customize the necessary tools for your app under the **Add features and functionality** section.

8. Proceed to the installation section and click **Install to Workspace**. You will be redirected to a page where you can choose a channel for your app’s functionalities. After choosing, click **Allow**.

![Install an app to workspace](https://docs.aembit.io/_astro/slack_install_app_to_workspace.CRoAIofo_FkIau.webp)

9. Select the **OAuth & Permissions** link from the left menu.

10. Click **Copy** to securely store the token for later use in the tenant configuration. For detailed information on OAuth tokens, please refer to the [official Slack documentation](https://api.slack.com/authentication/oauth-v2).

![Copy OAuth Token](https://docs.aembit.io/_astro/slack_copy_oauth_token.3BI6Lnf4_Z207Ixr.webp)

11. Create a new Credential Provider.

* **Name** - Choose a user-friendly name.
* **Credential Type** - [API Key](../../credential-providers/api-key.md)
* **API Key** - Paste the token copied from Slack.

## Client Workload Configuration

[Section titled “Client Workload Configuration”](#client-workload-configuration)

Aembit now handles the credentials required to access the Server Workload, eliminating the need for you to manage them directly. You can safely remove any previously used credentials from the Client Workload.

If you access the Server Workload through an SDK or library, it is possible that the SDK/library may still require credentials to be present for initialization purposes. In this scenario, you can provide placeholder credentials. Aembit will overwrite these placeholder credentials with the appropriate ones during the access process.

## Access Policy

[Section titled “Access Policy”](#access-policy)

* Create an Access Policy for a Client Workload to access the Slack Server Workload. Assign the newly created Credential Provider to this Access Policy.

## Required Features

[Section titled “Required Features”](#required-features)

* You will need to configure the [TLS Decrypt](../../../deploy-install/advanced-options/tls-decrypt/configure-tls-decrypt.md) feature to work with the Slack Server Workload.

## Related

**Compatible credential providers**

* [API Key](../../credential-providers/api-key.md)
* [OAuth 2.0 Client Credentials](../../credential-providers/oauth-client-credentials.md)
