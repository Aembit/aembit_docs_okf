---
type: how-to
title: "ChatGPT (OpenAI)"
description: "This page describes how to configure Aembit to work with the OpenAI Server Workload"
resource: https://docs.aembit.io/user-guide/access-policies/server-workloads/guides/openai/
interface: web-ui
tags: [ai-ml, guide, server-workload, access-policy]
timestamp: 2026-07-07T15:50:08-04:00
type_inferred: true
---

# ChatGPT (OpenAI)

This guide walks you through creating a Server Workload**Server Workload**: Server Workloads represent target services, APIs, databases, or applications that receive and respond to access requests from Client Workloads.[Learn more](../../../../get-started/concepts/server-workloads.md) in Aembit to securely access the [OpenAI](https://platform.openai.com/) API without storing static API keys in your applications.

Use this Server Workload to let your applications call OpenAI for tasks such as text completion, summarization, and sentiment analysis, while Aembit manages and injects the credential at access time.

## Create a project API key

[Section titled “Create a project API key”](#create-a-project-api-key)

To configure this Server Workload, you need an OpenAI account and a project API key. If you already have an API key, skip to [Server Workload configuration](#server-workload-configuration). For more details on API key authentication, see the [official OpenAI API documentation](https://platform.openai.com/docs/api-reference/api-keys).

1. Sign in to your OpenAI account.

2. Go to the [API Keys](https://platform.openai.com/api-keys) page from the left menu.

3. Click **Create new secret key** in the middle of the page.

4. In the pop-up window, set **Owned by** and choose the **Project**. If you don’t have multiple projects, OpenAI selects **Default Project**. Then fill in either the optional **Name** field or the **Service account ID**, depending on the **Owned by** selection.

   * If you set **Owned by** to **You**, under the **Permissions** section, select the permissions (scopes) for your application.
   * Click **Create secret key** to proceed.

   ![Create secret key](https://docs.aembit.io/_astro/openai_api_create_secret_key.DNx9sQhl_Z5zm45.webp)

5. Click **Copy** and securely store the key for later use in your Aembit Tenant configuration.

   ![Copy secret key](https://docs.aembit.io/_astro/openai_api_copy_secret_key.DIZm_7L7_ZO84PB.webp)

Note

In the OpenAI console, project API keys offer more granular control over your OpenAI resources than user API keys. The Aembit configuration steps work with either type.

## Server Workload configuration

[Section titled “Server Workload configuration”](#server-workload-configuration)

Now that you’ve copied your OpenAI API key, return to your Aembit Tenant to create the Server Workload.

1. Go to **Server Workloads**, and click **+ New**.

2. Configure the following fields:

   | Field                     | Value                        |
   | ------------------------- | ---------------------------- |
   | **Name**                  | Choose a user-friendly name. |
   | **Host**                  | `api.openai.com`             |
   | **Application Protocol**  | HTTP                         |
   | **Port**                  | 443 with TLS                 |
   | **Forward to Port**       | 443 with TLS                 |
   | **Authentication method** | HTTP Authentication          |
   | **Authentication scheme** | Bearer                       |

3. Click **Save**.

## Credential Provider configuration

[Section titled “Credential Provider configuration”](#credential-provider-configuration)

In your Aembit Tenant, create a new Credential Provider with the following values:

* **Name** - Choose a user-friendly name.
* **Credential Type** - [API Key](../../credential-providers/api-key.md)
* **API Key** - Paste the key you copied from the OpenAI console.

Click **Save** to store the Credential Provider.

Use Workload Identity Federation for enhanced security

To use short-lived tokens instead, configure an [OpenAI WIF Credential Provider](../../credential-providers/openai-workload-identity-federation.md).

## Client Workload configuration

[Section titled “Client Workload configuration”](#client-workload-configuration)

Aembit handles the credentials required to access the Server Workload, so you don’t manage them directly. You can remove any previously used credentials from the Client Workload.

If you access the Server Workload through an SDK or library, the SDK or library might still require credentials for initialization purposes. In this scenario, you can provide placeholder credentials. Aembit overwrites these placeholder credentials with the appropriate ones during the access process.

## Access Policy

[Section titled “Access Policy”](#access-policy)

Create an Access Policy for a Client Workload to access the OpenAI Server Workload. Assign the newly created Credential Provider to this Access Policy.

## Required features

[Section titled “Required features”](#required-features)

Configure the [TLS Decrypt](../../../deploy-install/advanced-options/tls-decrypt/configure-tls-decrypt.md) feature to work with the OpenAI API Server Workload.

## Related

**Compatible credential providers**

* [API Key](../../credential-providers/api-key.md)
