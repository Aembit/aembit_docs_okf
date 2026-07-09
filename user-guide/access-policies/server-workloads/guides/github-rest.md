---
type: how-to
title: "GitHub REST"
description: "This page describes how to configure Aembit to work with the GitHub REST API Server Workload."
resource: https://docs.aembit.io/user-guide/access-policies/server-workloads/guides/github-rest/
interface: web-ui
tags: [ci-cd, guide, server-workload, access-policy]
timestamp: 2026-04-27T11:40:47-04:00
type_inferred: true
---

# GitHub REST


[GitHub](https://github.com/) is a cloud-based platform for code hosting and version control using Git. Its REST API enables programmatic interaction with GitHub’s features, allowing for custom tool development and automation.

Below you can find the Aembit configuration required to work with the GitHub service as a Server Workload using the GitHub REST API.

Aembit supports multiple authentication/authorization methods for GitHub. This page describes scenarios where the Credential Provider is configured for GitHub via:

* [OAuth 2.0 Authorization Code (3LO)](#oauth-20-authorization-code)
* [API Key](github-rest.md#api-key)

Prerequisites

Before proceeding with the configuration, ensure you have the following:

* A GitHub account
* A personal access token (API Key Method)
* A GitHub app (OAuth 2.0 Authorization Code Method)

If you have not created a token or an app before, you can follow the steps outlined in the subsequent sections. For detailed information on authenticating with different flows, please refer to the [official GitHub documentation](https://docs.github.com/en/rest/authentication/authenticating-to-the-rest-api?apiVersion=2022-11-28).

## OAuth 2.0 Authorization Code

[Section titled “OAuth 2.0 Authorization Code”](#oauth-20-authorization-code)

### Server Workload Configuration

[Section titled “Server Workload Configuration”](#server-workload-configuration)

1. Create a new Server Workload.

* **Name** - Choose a user-friendly name.

2. Configure the service endpoint:

* **Host** - `api.github.com`
* **Application Protocol** - HTTP
* **Port** - 443 with TLS
* **Forward to Port** - 443 with TLS
* **Authentication method** - HTTP Authentication
* **Authentication scheme** - Bearer

### Credential Provider Configuration

[Section titled “Credential Provider Configuration”](#credential-provider-configuration)

1. Sign in to your GitHub account.

2. In the upper-right corner of any page, click your profile photo, then click **Settings**.

3. Navigate to **Developer settings** in the left-hand menu, and choose **Github Apps**.

4. On the right side, click on the **New GitHub App** button.

![Create New Github App](https://docs.aembit.io/_astro/github_create_github_app.CP8xQolE_1S4K0Y.webp)

5. Provide a name for your app, and optionally type a description of your app.

6. For the **Homepage URL**, enter the full URL of your Aembit Tenant (e.g., `https://xyz.aembit.io`,).

7. Switch to the Aembit UI to create a new Credential Provider, selecting the OAuth 2.0 Authorization Code credential type. After setting up the Credential Provider, copy the **Callback URL**.

8. Return to GitHub and under **Callback URL**, paste the copied URL.

9. Check the **Request user authorization** box and uncheck the **webhook**.

10. Under the **Permissions** section, expand the drop-down menus and select the permissions (scopes) for your application depending on your needs.

11. Choose the installation area for this app, then click on **Create Github App**.

12. Copy the **Client ID**, then click **Generate a new client secret**, and copy the **Client Secret**. Securely store the token for later use in the configuration on the tenant.

![GitHub App Copy Client ID and Client Secret](https://docs.aembit.io/_astro/github_app_copy_clientid_and_secret.C2MjIgt5_Z2he8L5.webp)

13. Edit the existing Credential Provider created in the previous steps.

* **Name** - Choose a user-friendly name.
* **Credential Type** - [OAuth 2.0 Authorization Code](../../credential-providers/oauth-authorization-code.md)
* **Callback URL (Read-Only)** - Aembit auto-generates this read-only URL.
* **Client Id** - Provide the Client ID copied from GitHub.
* **Client Secret** - Provide the Secret copied from GitHub.
* **Scopes** - You can leave this field empty by entering a single whitespace, as GitHub will default to your selected scopes for the app.
* **OAuth URL** - `https://github.com`
* **Authorization URL** - `https://github.com/login/oauth/authorize`
* **Token URL** - `https://github.com/login/oauth/access_token`
* **PKCE Required** - Off (PKCE is not supported by Github, so leave this field unchecked).
* **Lifetime** - 6 Months

14. Click **Save** to save your changes on the Credential Provider.

15. In the Aembit UI, click the **Authorize** button. You are be directed to a page where you can review the access request. Click **Authorize** to complete the OAuth 2.0 Authorization Code flow. You should see a success page and be redirected to Aembit automatically. You can also verify your flow is complete by checking the **State** value in the Credential Provider. After completion, it should be in a **Ready** state.

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

* **Host** - `api.github.com`
* **Application Protocol** - HTTP
* **Port** - 443 with TLS
* **Forward to Port** - 443 with TLS
* **Authentication method** - HTTP Authentication
* **Authentication scheme** - Bearer

### Credential Provider Configuration

[Section titled “Credential Provider Configuration”](#credential-provider-configuration-1)

1. Sign in to your GitHub account.

2. In the upper-right corner of any page, click your profile photo, then click **Settings**.

3. Navigate to **Developer settings** in the left-hand menu.

4. Under **Personal access tokens**, choose **Fine-grained tokens**.

5. On the right side, click on the **Generate new token** button.

![Generate new fine-grained token](https://docs.aembit.io/_astro/github_rest_create_fine_grained_token.DtiXp9EW_1yaR2u.webp)

6. Provide a name, expiration date, and description for your token. Choose the resource owner and repository access type.

7. Under the **Permissions** section, expand the drop-down menu and select the permissions (scopes) for your application depending on your needs.

8. After making all of your selections, click on **Generate Token**.

9. Click **Copy to clipboard** and securely store the token for later use in the configuration on the tenant.

![Copy fine-grained token](https://docs.aembit.io/_astro/github_rest_copy_fine_grained_token.D0fWLkgl_6DIXM.webp)

Note

The following configuration steps also work with classic personal access tokens; however, fine-grained tokens are recommended as they offer more granular permissions and improved security.

10. Create a new Credential Provider.

* **Name** - Choose a user-friendly name.
* **Credential Type** - [API Key](../../credential-providers/api-key.md)
* **API Key** - Paste the token copied from GitHub.

## Client Workload Configuration

[Section titled “Client Workload Configuration”](#client-workload-configuration)

Aembit now handles the credentials required to access the Server Workload, eliminating the need for you to manage them directly. You can safely remove any previously used credentials from the Client Workload.

If you access the Server Workload through an SDK or library, it is possible that the SDK/library may still require credentials to be present for initialization purposes. In this scenario, you can provide placeholder credentials. Aembit will overwrite these placeholder credentials with the appropriate ones during the access process.

## Access Policy

[Section titled “Access Policy”](#access-policy)

* Create an Access Policy for a Client Workload to access the GitHub REST API Server Workload. Assign the newly created Credential Provider to this Access Policy.

## Required Features

[Section titled “Required Features”](#required-features)

* You will need to configure the [TLS Decrypt](../../../deploy-install/advanced-options/tls-decrypt/configure-tls-decrypt.md) feature to work with the GitHub REST API Server Workload.

## Related

**Compatible credential providers**

* [API Key](../../credential-providers/api-key.md)
* [OAuth 2.0 Client Credentials](../../credential-providers/oauth-client-credentials.md)
