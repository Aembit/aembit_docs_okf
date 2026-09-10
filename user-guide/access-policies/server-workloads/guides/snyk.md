---
type: how-to
title: "Snyk"
description: "This page describes how to configure Aembit to work with the Snyk Server Workload."
resource: https://docs.aembit.io/user-guide/access-policies/server-workloads/guides/snyk/
interface: web-ui
tags: ["security", "guide", "server-workload", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Snyk


[Snyk](https://snyk.io/) is a security platform designed to help organizations find and fix vulnerabilities in their code, dependencies, containers, and infrastructure as code. It integrates into development workflows to maintain security throughout the software development lifecycle.

Below you can find the Aembit configuration required to work with the Snyk service as a Server Workload using the Snyk API.

> **Prerequisites**
>
> Before proceeding with the configuration, you need to have a Snyk tenant and an authorized Snyk App. If you have not created an app before, you can follow the steps outlined in the subsequent sections. For detailed information on how to create a Snyk App using the Snyk API or other methods, please refer to the [official Snyk documentation](https://docs.snyk.io/snyk-api/snyk-apps/create-a-snyk-app-using-the-snyk-api).

## Server Workload Configuration

1. Create a new Server Workload.

* **Name** - Choose a user-friendly name.

2. Configure the service endpoint:

* **Host** - `api.snyk.io`
* **Application Protocol** - HTTP
* **Port** - 443 with TLS
* **Forward to Port** - 443 with TLS
* **Authentication method** - HTTP Authentication
* **Authentication scheme** - Bearer

## Credential Provider Configuration

1. Sign in to your Snyk account.

2. In the lower-left corner of any page, click your profile name, then click **Account Settings**.

3. On the **General** page, click to reveal your **Key**.

4. Copy the **Key** and securely store it for later use in the app creation process using the Snyk API.

![Snyk Copy Key](https://docs.aembit.io/_astro/snyk_get_auth_token.B6BuN9GY_ZO4Tyr.webp)

5. Navigate to **Settings** in the left-hand menu, and choose **General**.

6. Copy the **Organization ID** and securely store it for later use in the app creation process using the Snyk API.

![Snyk Copy Organization ID](https://docs.aembit.io/_astro/snyk_get_organization_id.CfUVqlp9_Z1oPHk8.webp)

7. Switch to the Aembit UI to create a new Credential Provider, selecting the OAuth 2.0 Authorization Code credential type. After setting it up, copy the **Callback URL**.

8. Create a Snyk App:

To create a Snyk App, execute the following `curl` command. Make sure to replace the placeholders with the appropriate values:

* REPLACE\_WITH\_API\_TOKEN: This is the token you retrieved in Step 4.
* REPLACE\_WITH\_APP\_NAME: Provide a friendly name for your app that will perform OAuth with Snyk, such as “Aembit.”
* REPLACE\_WITH\_CALLBACK\_URL: Use the callback URL obtained in the previous step.
* REPLACE\_WITH\_SCOPES: Add the necessary scopes for your app. It is crucial to include the `org.read` scope, which is required for the refresh token. For a comprehensive list of available scopes, refer to the [official Snyk documentation](https://docs.snyk.io/snyk-api/snyk-apps/scopes-to-request).
* REPLACE\_WITH\_YOUR\_ORGID: This is the organization ID you retrieved in Step 6.

```shell
curl -X POST -H "Content-Type: application/vnd.api+json" \
    -H "Authorization: token <REPLACE_WITH_API_TOKEN>" \
    -d '{"data": { "attributes": {"name": "<REPLACE_WITH_APP_NAME>", "redirect_uris": ["<REPLACE_WITH_CALLBACK_URL>"], "scopes": ["<REPLACE_WITH_SCOPES>"], "context": "user"}, "type": "app"}}' \
    "https://api.snyk.io/rest/orgs/<REPLACE_WITH_YOUR_ORGID>/apps/creations?version=2024-01-04"
```

The response includes important configuration details, such as the **clientId** and **clientSecret**, which are essential for completing the authorizing of your Snyk App.

9. Edit the existing Credential Provider created in the previous steps.

* **Name** - Choose a user-friendly name.
* **Credential Type** - [OAuth 2.0 Authorization Code](../../credential-providers/oauth-authorization-code.md)
* **Callback URL (Read-Only)** - Aembit auto-generates this read-only URL.
* **Client Id** - Provide the `clientId` from the response of the `curl` command.
* **Client Secret** - Provide the `clientSecret` from the response of the `curl` command.
* **Scopes** - Enter the scopes you use, space delimited. (e.g. `org.read org.project.read org.project.snapshot.read`)
* **OAuth URL** - `https://snyk.io/`
* **Authorization URL** - `https://app.snyk.io/oauth2/authorize`
* **Token URL** - `https://api.snyk.io/oauth2/token`
* **PKCE Required** - On
* **Lifetime** - 1 year (Snyk does not specify a refresh token lifetime; this value is recommended by Aembit.)

10. Click **Save** to save your changes on the Credential Provider.

11. In the Aembit UI, click the **Authorize** button. You are directed to a page where you can review the access request. Click **Authorize** to complete the OAuth 2.0 Authorization Code flow. You should see a success page and then be redirected to Aembit automatically. You can also verify your flow is complete by checking the **State** value in the Credential Provider. After completion, it should be **Ready**.

![Credential Provider - Ready State](https://docs.aembit.io/_astro/credential_providers_auth_code_status_ready.CBPCBiJg_ZBh135.webp)

> **Caution**
>
> Once the set lifetime ends, the retrieved credential will expire and will not work anymore. Aembit will notify you before this happens. Please ensure you reauthorize the credential before it expires.

## Client Workload Configuration

Aembit now handles the credentials required to access the Server Workload, eliminating the need for you to manage them directly. You can safely remove any previously used credentials from the Client Workload.

If you access the Server Workload through an SDK or library, it is possible that the SDK/library may still require credentials to be present for initialization purposes. In this scenario, you can provide placeholder credentials. Aembit will overwrite these placeholder credentials with the appropriate ones during the access process.

## Access Policy

* Create an access policy for a Client Workload to access the Snyk Server Workload and assign the newly created Credential Provider to it.

## Required Features

* You will need to configure the [TLS Decrypt](../../../deploy-install/advanced-options/tls-decrypt/configure-tls-decrypt.md) feature to work with the Snyk Server Workload.

## Related

**Compatible credential providers**

* [API Key](../../credential-providers/api-key.md)
