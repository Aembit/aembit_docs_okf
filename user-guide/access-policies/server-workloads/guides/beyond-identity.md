---
type: how-to
title: "Beyond Identity"
description: "This page describes how to configure Aembit to work with the Beyond Identity Server Workload."
resource: https://docs.aembit.io/user-guide/access-policies/server-workloads/guides/beyond-identity/
interface: web-ui
tags: ["security", "guide", "server-workload", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Beyond Identity


[Beyond Identity](https://www.beyondidentity.com/) is a passwordless authentication service designed to bolster security measures for various applications and platforms. The Beyond Identity API serves as a developer-friendly interface, enabling seamless integration of advanced cryptographic techniques to eliminate reliance on traditional passwords.

Below you can find the Aembit configuration required to work with the Beyond Identity service as a Server Workload using the Beyond Identity API.

## Prerequisites

Before proceeding with the configuration, ensure you have the following:

* Beyond Identity tenant.
* An app configured in your Beyond Identity tenant. This can either be a custom application you set up or the built-in **Beyond Identity Management API app**. If you have not configured an app yet, follow the steps outlined in the next section or refer to the [official Beyond Identity documentation](https://developer.beyondidentity.com/docs/add-an-application) for more detailed instructions.

### Add new app in Beyond Identity

1. Log in to the [Beyond Identity Admin Console](https://console-us.beyondidentity.com/login).

2. Navigate to the left pane, select **Apps**, and then click on **Add an application** from the top-right corner.

![Beyond Identity Add an App](https://docs.aembit.io/_astro/beyond_identity_add_app.DQXPoSyi_1f35q.webp)

3. Configure the app based on your preferences. Below are key choices:

* Enter a name for the **Display Name**.
* Choose **OAuth2** for the Protocol under **Client Configuration**.
* Choose **Confidential** for the Client Type.
* Choose **Disabled** for the PKCE.
* Choose **Client Secret Basic** for the Token Endpoint Auth Method.
* Select **Client Credentials** for the Grant Type.
* Optionally, choose the scopes you intend to use in the **Token Configuration** section under **Allowed Scopes**.

4. After making your selections, click **Submit** to save the new app.

## Server Workload Configuration

1. Create a new Server Workload.

* **Name** - Choose a user-friendly name.

2. Configure the service endpoint:

* **Host** - `api-us.beyondidentity.com`
* **Application Protocol** - HTTP
* **Port** - 443 with TLS
* **Forward to Port** - 443 with TLS
* **Authentication method** - HTTP Authentication
* **Authentication scheme** - Bearer

## Credential Provider Configuration

1. Log in to the [Beyond Identity Admin Console](https://console-us.beyondidentity.com/login).

2. Navigate to the left pane and select **Apps** to access a list of your applications within your realm.

3. Choose your pre-configured application or use the default **Beyond Identity Management API** app.

4. In the External Protocol tab, copy the **Token Endpoint**. From the Client Configuration section, also copy both the **Client ID** and **Client Secret**. Keep these details stored for later use in the tenant configuration.

![App Details | Copy Token Endpoint, Client ID and Tenant ID](https://docs.aembit.io/_astro/beyond_identity_app_details.BnmxUipL_m4lUm.webp)

5. Create a new Credential Provider.

* **Name** - Choose a user-friendly name.
* **Credential Type** - [OAuth 2.0 Client Credentials](../../credential-providers/oauth-client-credentials.md)
* **Token endpoint** - Provide the token endpoint copied from Beyond Identity.
* **Client ID** - Provide the client ID copied from Beyond Identity.
* **Client Secret** - Provide the client secret copied from Beyond Identity.
* **Scopes** - Enter the scopes you use, space delimited. (You can find scopes in the App details, Token Configuration section under **Allowed Scopes**)

## Client Workload Configuration

Aembit now handles the credentials required to access the Server Workload, eliminating the need for you to manage them directly. You can safely remove any previously used credentials from the Client Workload.

If you access the Server Workload through an SDK or library, it is possible that the SDK/library may still require credentials to be present for initialization purposes. In this scenario, you can provide placeholder credentials. Aembit will overwrite these placeholder credentials with the appropriate ones during the access process.

## Access Policy

* Create an Access Policy for a Client Workload to access the Beyond Identity Server Workload. Assign the newly created Credential Provider to this Access Policy.

## Required Features

* You will need to configure the [TLS Decrypt](../../../deploy-install/advanced-options/tls-decrypt/configure-tls-decrypt.md) feature to work with the Beyond Identity Server Workload.

## Related

**Compatible credential providers**

* [API Key](../../credential-providers/api-key.md)
* [OAuth 2.0 Client Credentials](../../credential-providers/oauth-client-credentials.md)
