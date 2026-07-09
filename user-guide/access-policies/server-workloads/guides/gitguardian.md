---
type: how-to
title: "GitGuardian"
description: "This page describes how to configure Aembit to work with the GitGuardian Server Workload."
resource: https://docs.aembit.io/user-guide/access-policies/server-workloads/guides/gitguardian/
interface: web-ui
tags: [security, guide, server-workload, access-policy]
timestamp: 2025-05-29T11:26:12-07:00
type_inferred: true
---

# GitGuardian


[GitGuardian](https://www.gitguardian.com/) is a cybersecurity platform dedicated to safeguarding sensitive information within source code repositories. It specializes in identifying and protecting against potential data leaks, ensuring that organizations maintain the confidentiality of their critical data.

Below you can find the Aembit configuration required to work with the GitGuardian service as a Server Workload using the GitGuardian API.

## Prerequisites

[Section titled “Prerequisites”](#prerequisites)

Before proceeding with the configuration, you will need to have a GitGuardian tenant (or [sign up](https://dashboard.gitguardian.com/auth/signup) for one).

## Server Workload Configuration

[Section titled “Server Workload Configuration”](#server-workload-configuration)

1. Create a new Server Workload.

* **Name** - Choose a user-friendly name.

2. Configure the service endpoint:

* **Host** - `api.gitguardian.com`
* **Application Protocol** - HTTP
* **Port** - 443 with TLS
* **Forward to Port** - 443 with TLS
* **Authentication method** - API Key
* **Authentication scheme** - Header
* **Header** - Authorization

## Credential Provider Configuration

[Section titled “Credential Provider Configuration”](#credential-provider-configuration)

1. Navigate to the [GitGuardian Dashboard](https://dashboard.gitguardian.com/) and sign in with your account.

2. On the left sidebar, choose **API** and then go to **Personal access tokens** in the second left pane to access details.

3. Click on **Create Token** in the top right corner.

4. Provide a name, choose an expiration time, select scopes based on your preferences, and then click **Create token** at the bottom of the modal.

![Create GitGuardian API Personal Access token](https://docs.aembit.io/_astro/gitguardian_key.D-rGJ8fw_2szqSa.webp)

5. Make sure to copy your new personal access token at this stage, as it will not be visible again. For more information on authentication, please refer to the [official GitGuardian API documentation](https://api.gitguardian.com/docs#section/Authentication).

6. Create a new Credential Provider.

* **Name** - Choose a user-friendly name.
* **Credential Type** - [API Key](../../credential-providers/api-key.md)
* **API Key** - Provide the key copied from GitGuardian and use the format `Token api-key`, replacing `api-key` with your API key.

## Client Workload Configuration

[Section titled “Client Workload Configuration”](#client-workload-configuration)

Aembit now handles the credentials required to access the Server Workload, eliminating the need for you to manage them directly. You can safely remove any previously used credentials from the Client Workload.

If you access the Server Workload through an SDK or library, it is possible that the SDK/library may still require credentials to be present for initialization purposes. In this scenario, you can provide placeholder credentials. Aembit will overwrite these placeholder credentials with the appropriate ones during the access process.

## Access Policy

[Section titled “Access Policy”](#access-policy)

* Create an access policy for a Client Workload to access the GitGuardian Server Workload and assign the newly created Credential Provider to it.

## Required Features

[Section titled “Required Features”](#required-features)

* You will need to configure the [TLS Decrypt](../../../deploy-install/advanced-options/tls-decrypt/configure-tls-decrypt.md) feature to work with the GitGuardian Server Workload.

## Related

**Compatible credential providers**

* [API Key](../../credential-providers/api-key.md)
