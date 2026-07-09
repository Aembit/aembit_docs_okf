---
type: how-to
title: "PayPal"
description: "This page describes how to configure Aembit to work with the PayPal Server Workload."
resource: https://docs.aembit.io/user-guide/access-policies/server-workloads/guides/paypal/
interface: web-ui
tags: [financial, guide, server-workload, access-policy]
timestamp: 2025-05-29T11:26:12-07:00
type_inferred: true
---

# PayPal


[PayPal](https://www.paypal.com/) is an online payment platform that allows individuals and businesses to send and receive payments securely. PayPal supports various payment methods, including credit cards, debit cards, and bank transfers.

Below you can find the Aembit configuration required to work with the PayPal service as a Server Workload using the PayPal REST API.

## Prerequisites

[Section titled “Prerequisites”](#prerequisites)

Before proceeding with the configuration, you will need to have a PayPal Developer tenant (or [sign up](https://www.paypal.com/signin) for one).

## Server Workload Configuration

[Section titled “Server Workload Configuration”](#server-workload-configuration)

1. Create a new Server Workload.

* **Name** - Choose a user-friendly name.

2. Configure the service endpoint:

* **Host** - `api-m.sandbox.paypal.com` (Sandbox) or `api-m.paypal.com` (Live)
* **Application Protocol** - HTTP
* **Port** - 443 with TLS
* **Forward to Port** - 443 with TLS
* **Authentication method** - HTTP Authentication
* **Authentication scheme** - Bearer

## Credential Provider Configuration

[Section titled “Credential Provider Configuration”](#credential-provider-configuration)

1. Log into the [PayPal Developer Dashboard](https://developer.paypal.com/dashboard/) using your PayPal account credentials.

2. Navigate to the [Apps & Credentials](https://developer.paypal.com/dashboard/applications/) page from the top menu.

3. Ensure you are in the correct mode (Sandbox mode for test data or Live mode for production data).

4. Locate the **Default Application** under the REST API apps list.

5. Click the copy buttons next to the **Client ID** and **Client Secret** values to copy them. Store these details securely for later use in the tenant configuration.

![Copy Client ID and Secret](https://docs.aembit.io/_astro/paypal_copy_client_id_and_secret.4I5wxghi_Z28Fgg9.webp)

6. Create a new Credential Provider.

* **Name** - Choose a user-friendly name.
* **Credential Type** - [OAuth 2.0 Client Credentials](../../credential-providers/oauth-client-credentials.md)
* **Token endpoint** - `https://api-m.sandbox.paypal.com/v1/oauth2/token` (Sandbox) or `https://api-m.paypal.com/v1/oauth2/token` (Live)
* **Client ID** - Provide the client ID copied from PayPal.
* **Client Secret** - Provide the client secret copied from PayPal.
* **Scopes** - You can leave this field **empty**, as PayPal will default to the necessary scopes, or specify the required scopes based on your needs, such as `https://uri.paypal.com/services/invoicing`. For more detailed information, you can refer to the [official PayPal Developer Documentation](https://developer.paypal.com/api/rest/authentication/).

## Client Workload Configuration

[Section titled “Client Workload Configuration”](#client-workload-configuration)

Aembit now handles the credentials required to access the Server Workload, eliminating the need for you to manage them directly. You can safely remove any previously used credentials from the Client Workload.

If you access the Server Workload through an SDK or library, it is possible that the SDK/library may still require credentials to be present for initialization purposes. In this scenario, you can provide placeholder credentials. Aembit will overwrite these placeholder credentials with the appropriate ones during the access process.

## Access Policy

[Section titled “Access Policy”](#access-policy)

* Create an access policy for a Client Workload to access the PayPal Server Workload and assign the newly created Credential Provider to it.

## Required Features

[Section titled “Required Features”](#required-features)

* You will need to configure the [TLS Decrypt](../../../deploy-install/advanced-options/tls-decrypt/configure-tls-decrypt.md) feature to work with the PayPal Server Workload.

## Related

**Compatible credential providers**

* [OAuth 2.0 Client Credentials](../../credential-providers/oauth-client-credentials.md)
