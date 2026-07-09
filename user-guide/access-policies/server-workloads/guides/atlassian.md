---
type: how-to
title: "Atlassian"
description: "This page describes how to configure Aembit to work with the Atlassian Server Workload."
resource: https://docs.aembit.io/user-guide/access-policies/server-workloads/guides/atlassian/
interface: web-ui
tags: [productivity, guide, server-workload, access-policy]
timestamp: 2026-04-27T11:40:47-04:00
type_inferred: true
---

# Atlassian


[Atlassian](https://www.atlassian.com/) is a cloud-based service offering that facilitates collaborative work and project management for teams by providing a suite of tools, which include:

* Jira for project tracking
* Confluence for document collaboration
* Bitbucket for version control; and
* other integrated applications

Below you can find the Aembit configuration required to work with the Atlassian Cloud service as a Server Workload using the Atlassian REST APIs. Aembit supports multiple authentication/authorization methods for Atlassian. This page describes scenarios where the Credential Provider is configured for Atlassian via:

* [OAuth 2.0 Authorization Code (3LO)](atlassian.md#oauth-20-authorization-code)
* [API Key](atlassian.md#api-key)

Prerequisites

Before proceeding with the configuration, you will need to have an Atlassian tenant and related Atlassian Developer account.

## OAuth 2.0 Authorization Code

[Section titled “OAuth 2.0 Authorization Code”](#oauth-20-authorization-code)

### Server Workload Configuration

[Section titled “Server Workload Configuration”](#server-workload-configuration)

1. Create a new Server Workload.

* **Name** - Choose a user-friendly name.

2. Configure the service endpoint:

* **Host** - `<domain>.atlassian.net`
* **Application Protocol** - HTTP
* **Port** - 443 with TLS
* **Forward to Port** - 443 with TLS
* **Authentication method** - HTTP Authentication
* **Authentication scheme** - Bearer

### Credential Provider Configuration

[Section titled “Credential Provider Configuration”](#credential-provider-configuration)

1. Log into to the [Atlassian Developer Console](https://developer.atlassian.com/console/myapps/).

2. Click on **Create** and select the **OAuth 2.0 integration** option.

![Create an App](https://docs.aembit.io/_astro/atlassian_developer_console_create_app.DUW4s_9t_ZK5jhV.webp)

3. Provide a name for your app, check the agreement box, and click **Create** .

4. In the left pane, select **Authorization**, and then click **Add** under the Action column.

5. Switch to the Aembit UI to create a new Credential Provider, selecting the OAuth 2.0 Authorization Code credential type. After setting up the Credential Provider, copy the **Callback URL**.

6. Return to Atlassian and paste the copied URL into the **Callback URL** field.

7. In the left pane, select **Permissions**, and then click **Add** under the Action column of the API that best suits your project needs. After clicking **Add**, it will change to **Configure**; click **Configure** to edit.

![Atlassian Scopes](https://docs.aembit.io/_astro/atlassian_permissions.HYNhBqUi_ZBlEHJ.webp)

8. On the redirected page, click **Edit Scopes**, add the necessary scopes for your application, and then click **Save** Copy the **Code** version of all selected scopes and save this information for future use.

9. In the left pane, select **Settings**, scroll down to the **Authentication details**, and copy both the **Client ID** and the **Secret**. Store them for later use in the tenant configuration.

![Copy Client ID and Client Secret](https://docs.aembit.io/_astro/atlassian_copy_client_id_and_secret.Bz55I8Z-_Z1Efz89.webp)

10. Edit the existing Credential Provider created in the previous steps.

* **Name** - Choose a user-friendly name.
* **Credential Type** - [OAuth 2.0 Authorization Code](../../credential-providers/oauth-authorization-code.md)
* **Callback URL (Read-Only)** - Aembit auto-generates this read-only URL.
* **Client Id** - Provide the Client ID copied from Atlassian.
* **Client Secret** - Provide the Secret copied from Atlassian.
* **Scopes** - Enter the scopes you use, space delimited. Must include the `offline_access` scope required for the refresh token (e.g. `offline_access read:jira-work read:servicedesk-request`)
* **OAuth URL** - `https://auth.atlassian.com`

Click on **URL Discovery** to populate the Authorization and Token URL fields, which can be left as populated.

* **PKCE Required** - Off (PKCE is not supported by Atlassian, so leave this field unchecked).
* **Lifetime** - 1 year (Absolute expiry time according to Atlassian)\
  For more information on rotating the refresh token, please refer to the [official Atlassian documentation](https://developer.atlassian.com/cloud/jira/platform/oauth-2-3lo-apps/#use-a-refresh-token-to-get-another-access-token-and-refresh-token-pair).

11. Click **Save** to save your changes on the Credential Provider.

12. In Aembit UI, click the **Authorize** button. You are be directed to a page where you can review the access request. Click **Accept** to complete the OAuth 2.0 Authorization Code flow. You should see a success page and be redirected to Aembit automatically. You can also verify that your flow is complete by checking the **State** value in the Credential Provider. After completion, it should be in a **Ready** state.

![Credential Provider - Ready State](https://docs.aembit.io/_astro/credential_providers_auth_code_status_ready.CBPCBiJg_ZBh135.webp)

Caution

Once the set lifetime ends, the retrieved credential will expire and no longer be active. Aembit will notify you before this happens. Please ensure you reauthorize your credential before it expires.

## API Key

[Section titled “API Key”](#api-key)

Note

This section is labeled as API Key because, while it requires a username (your Atlassian email) and password, the password is actually an API key. Atlassian uses HTTP Basic Authentication, and we use the Username & Password Credential Provider in Aembit UI to implement this method.

### Server Workload Configuration

[Section titled “Server Workload Configuration”](#server-workload-configuration-1)

1. Create a new Server Workload.

* **Name** - Choose a user-friendly name.

2. Configure the service endpoint:

* **Host** - `<domain>.atlassian.net`
* **Application Protocol** - HTTP
* **Port** - 443 with TLS
* **Forward to Port** - 443 with TLS
* **Authentication method** - HTTP Authentication
* **Authentication scheme** - Basic

### Credential Provider Configuration

[Section titled “Credential Provider Configuration”](#credential-provider-configuration-1)

1. Sign into your Atlassian account.

2. Navigate to the [Atlassian account - API Tokens](https://id.atlassian.com/manage-profile/security/api-tokens) page.

3. Click on **Create API token**.

4. In the dialog that appears, enter a memorable and concise label for your token, and then click **Create**.

![Create Atlassian API token](https://docs.aembit.io/_astro/atlassian_api_tokens.CH6dhA6H_Z1R9QGq.webp)

5. Click **Copy to clipboard** and securely store the token for later use in the configuration on the tenant. For more information on how to store your API token, please refer to the [official Atlassian documentation](https://support.atlassian.com/atlassian-account/docs/manage-api-tokens-for-your-atlassian-account/).

6. Create a new Credential Provider.

* **Name** - Choose a user-friendly name.
* **Credential Type** - [Username & Password](../../credential-providers/username-password.md)
* **Username** - Your email address for the Atlassian account used to create the token.
* **Password** - Provide the token copied from Atlassian.

## Client Workload Configuration

[Section titled “Client Workload Configuration”](#client-workload-configuration)

Aembit now handles the credentials required to access the Server Workload, eliminating the need for you to manage them directly. You can safely remove any previously used credentials from the Client Workload.

If you access the Server Workload through an SDK or library, it is possible that the SDK/library may still require credentials to be present for initialization purposes. In this scenario, you can provide placeholder credentials. Aembit will overwrite these placeholder credentials with the appropriate ones during the access process.

## Access Policy

[Section titled “Access Policy”](#access-policy)

* Create an Access Policy for a Client Workload to access the Atlassian Server Workload. Assign the newly created Credential Provider to this Access Policy.

## Required Features

[Section titled “Required Features”](#required-features)

* You will need to configure the [TLS Decrypt](../../../deploy-install/advanced-options/tls-decrypt/configure-tls-decrypt.md) feature to work with the Atlassian Server Workload.

## Related

**Compatible credential providers**

* [API Key](../../credential-providers/api-key.md)
