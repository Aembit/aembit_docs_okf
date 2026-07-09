---
type: how-to
title: "Okta"
description: "This page describes how to configure Aembit to work with the Okta Server Workload."
resource: https://docs.aembit.io/user-guide/access-policies/server-workloads/guides/okta/
interface: web-ui
tags: [security, guide, server-workload, access-policy]
timestamp: 2025-05-29T11:26:12-07:00
type_inferred: true
---

# Okta


[Okta](https://www.okta.com/) is a cloud-based Identity and Access Management (IAM) platform that offers tools for user authentication, access control, and security, helping streamline identity management and improve user experiences across applications and devices.

Below you can find the Aembit configuration required to work with the Okta Workforce Identity Cloud service as a Server Workload using the Core Okta API.

## Prerequisites

[Section titled “Prerequisites”](#prerequisites)

Before proceeding with the configuration, you must have an Okta Workforce Identity Cloud organization (tenant).

## Server Workload Configuration

[Section titled “Server Workload Configuration”](#server-workload-configuration)

To retrieve the connection information in the Okta Admin Console:

* Click on your username in the upper-right corner of the Admin Console. The domain appears in the dropdown menu; copy the domain.

![Okta Endpoint](https://docs.aembit.io/_astro/okta_endpoint.yg4kq-xm_Z1tXDIy.webp)

1. Create a new Server Workload.

* **Name** - Choose a user-friendly name.

2. Configure the service endpoint:

* **Host** - `<subdomain>.okta.com` (Provide the domain copied from Okta)
* **Application Protocol** - HTTP
* **Port** - 443 with TLS
* **Forward to Port** - 443 with TLS
* **Authentication method** - API Key
* **Authentication scheme** - Header
* **Header** - Authorization

## Credential Provider Configuration

[Section titled “Credential Provider Configuration”](#credential-provider-configuration)

1. Sign in to your Okta organization as a user with administrator privileges.

2. In the left sidebar, select **Security**, then click on **API**.

3. Navigate to the **Tokens** tab in the ribbon list.

4. Click **Create Token**, name your token, and then click **Create Token**.

5. Click the **Copy to Clipboard icon** to securely store the token for later use in the tenant configuration. For detailed information on API tokens, please refer to the [official Okta documentation](https://developer.okta.com/docs/guides/create-an-api-token/main/).

![Copy API Token](https://docs.aembit.io/_astro/okta_copy_api_token.DBISsOnu_Z1fjLbh.webp)

6. Create a new Credential Provider.

* **Name** - Choose a user-friendly name.
* **Credential Type** - [API Key](../../credential-providers/api-key.md)
* **API Key** - Provide the key copied from Okta and use the format `SSWS api-token`, replacing `api-token` with your API token.

## Client Workload Configuration

[Section titled “Client Workload Configuration”](#client-workload-configuration)

Aembit now handles the credentials required to access the Server Workload, eliminating the need for you to manage them directly. You can safely remove any previously used credentials from the Client Workload.

If you access the Server Workload through an SDK or library, it is possible the SDK/library may still require credentials to be present for initialization purposes. In this scenario, you can provide placeholder credentials. Aembit will overwrite these placeholder credentials with the appropriate ones during the access process.

## Access Policy

[Section titled “Access Policy”](#access-policy)

* Create an Access Policy for a Client Workload to access the Okta Server Workload. Assign the newly created Credential Provider to this Access Policy.

## Required Features

[Section titled “Required Features”](#required-features)

* You will need to configure the [TLS Decrypt](../../../deploy-install/advanced-options/tls-decrypt/configure-tls-decrypt.md) feature to work with the Okta Server Workload.

## Related

**Compatible credential providers**

* [API Key](../../credential-providers/api-key.md)
* [OAuth 2.0 Client Credentials](../../credential-providers/oauth-client-credentials.md)
* [OIDC ID Token](../../credential-providers/oidc-id-token.md)
