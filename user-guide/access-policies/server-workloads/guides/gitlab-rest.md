---
type: how-to
title: "GitLab REST"
description: "This page describes how to configure Aembit to work with the GitLab REST API Server Workload."
resource: https://docs.aembit.io/user-guide/access-policies/server-workloads/guides/gitlab-rest/
interface: web-ui
tags: [ci-cd, guide, server-workload, access-policy]
timestamp: 2026-04-27T11:40:47-04:00
type_inferred: true
---

# GitLab REST


[GitLab](https://gitlab.com/) is a cloud-based DevOps lifecycle tool that provides a Git repository manager with features like CI/CD, issue tracking, and more. Its REST API allows for programmatic access to these features, enabling the development of custom tools and automation.

Below you can find the Aembit configuration required to work with the GitLab service as a Server Workload using the GitLab REST API.

Prerequisites

Before proceeding with the configuration, you must have a GitLab tenant (or [sign up](https://gitlab.com/users/sign_up) for one) and a user, group, or instance level owned application. If you have not generated an application yet, follow the configuration steps below. For detailed information on how to create a new application, please refer to the [official GitLab documentation](https://docs.gitlab.com/ee/integration/oauth_provider.html).

## Server Workload Configuration

[Section titled “Server Workload Configuration”](#server-workload-configuration)

1. Create a new Server Workload.

* **Name** - Choose a user-friendly name.

2. Configure the service endpoint:

* **Host** - `gitlab.com`
* **Application Protocol** - HTTP
* **Port** - 443 with TLS
* **Forward to Port** - 443 with TLS
* **Authentication method** - HTTP Authentication
* **Authentication scheme** - Bearer

## Credential Provider Configuration

[Section titled “Credential Provider Configuration”](#credential-provider-configuration)

1. Sign in to your GitLab account.

2. In the upper-left corner of any page, click your profile photo, then click **Edit Profile**.

3. Navigate to **Applications** in the left-hand menu.

4. On the right side, click on the **Add new application** button.

![Gitlab Add new application](https://docs.aembit.io/_astro/gitlab_create_app.rBQd5IO-_T4nnb.webp)

5. Provide a name for your app.

6. Switch to the Aembit UI to create a new Credential Provider, selecting the OAuth 2.0 Authorization Code credential type. After setting up the Credential Provider, copy the **Callback URL**.

7. Return to GitLab and paste the copied URL into the **Redirect URI** field.

8. Check the **Confidential** box, and select the scopes for your application depending on your needs.

9. After making all of your selections, click on **Save application**.

10. On the directed page, copy the **Application ID**, **Secret** and **Scopes**, and store them for later use in the tenant configuration.

![Gitlab New application](https://docs.aembit.io/_astro/gitlab_created_app.Knc8fkR7_1nkhRX.webp)

11. Edit the existing Credential Provider created in the previous steps.

* **Name** - Choose a user-friendly name.
* **Credential Type** - [OAuth 2.0 Authorization Code](../../credential-providers/oauth-authorization-code.md)
* **Callback URL (Read-Only)** - Aembit auto-generates this read-only URL.
* **Client Id** - Provide the Application ID copied from GitLab.
* **Client Secret** - Provide the Secret copied from GitLab.
* **Scopes** - Enter the scopes you use, space-delimited (e.g. `read_api read_user read_repository`).
* **OAuth URL** - `https://gitlab.com`

Click on **URL Discovery** to populate the Authorization and Token URL fields, which can be left as populated.

* **PKCE Required** - On
* **Lifetime** - 1 year (GitLab does not specify a refresh token lifetime; this value is recommended by Aembit.)

12. Click **Save** to save your changes on the Credential Provider.

13. In Aembit UI, click the **Authorize** button. You are directed to a page where you can review the access request. Click **Authorize** to complete the OAuth 2.0 Authorization Code flow. You should see a success page and be redirected to Aembit automatically. You can also verify your flow is complete by checking the **State** value in the Credential Provider. After completion, it should be in a **Ready** state.

![Credential Provider - Ready State](https://docs.aembit.io/_astro/credential_providers_auth_code_status_ready.CBPCBiJg_ZBh135.webp)

Caution

Once the set lifetime ends, the retrieved credential will expire and no longer be active. Aembit will notify you before this happens. Please ensure you reauthorize your credential before it expires.

## Client Workload Configuration

[Section titled “Client Workload Configuration”](#client-workload-configuration)

Aembit now handles the credentials required to access the Server Workload, eliminating the need for you to manage them directly. You can safely remove any previously used credentials from the Client Workload.

If you access the Server Workload through an SDK or library, it is possible that the SDK/library may still require credentials to be present for initialization purposes. In this scenario, you can provide placeholder credentials. Aembit will overwrite these placeholder credentials with the appropriate ones during the access process.

## Access Policy

[Section titled “Access Policy”](#access-policy)

* Create an Access Policy for a Client Workload to access the GitLab REST API Server Workload. Assign the newly created Credential Provider to this Access Policy.

## Required Features

[Section titled “Required Features”](#required-features)

* You will need to configure the [TLS Decrypt](../../../deploy-install/advanced-options/tls-decrypt/configure-tls-decrypt.md) feature to work with the GitLab REST API Server Workload.

## Related

**Compatible credential providers**

* [API Key](../../credential-providers/api-key.md)
* [OAuth 2.0 Client Credentials](../../credential-providers/oauth-client-credentials.md)
