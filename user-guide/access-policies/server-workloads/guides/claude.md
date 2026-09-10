---
type: how-to
title: "Claude"
description: "This page describes how to configure Aembit to work with the Claude Server Workload."
resource: https://docs.aembit.io/user-guide/access-policies/server-workloads/guides/claude/
interface: web-ui
tags: ["ai-ml", "guide", "server-workload", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Claude


[Claude](https://www.anthropic.com/api) is an artificial intelligence platform from Anthropic that allows developers to embed advanced language models into their applications. It supports tasks like natural language understanding and conversation generation, enhancing software functionality and user experience.

Below you can find the Aembit configuration required to work with the Claude service as a Server Workload using the Claude API and Anthropic’s Client SDKs.

## Prerequisites

Before proceeding with the configuration, ensure you have an Anthropic account and API key. If you have not already generated a key, follow the instructions below. For more details about Claude API, refer to the [official Claude API documentation](https://docs.anthropic.com/en/api/getting-started).

### Create API Key

1. Sign in to your Anthropic account.

2. Navigate to the [API Keys](https://console.anthropic.com/settings/keys) page by clicking the **Get API Keys** button from the dashboard menu.

![Anthropic Console Dashboard](https://docs.aembit.io/_astro/claude_api_dashboard.B6BRLfLw_2cfAe3.webp)

3. Click the **Create key** button in the top right corner of the page.

4. A pop-up window will appear. Fill in the name field, then click **Create Key** to proceed.

![Create API key](https://docs.aembit.io/_astro/claude_api_create_key.C8l1sCD-_Z1nqFxA.webp)

5. Click **Copy** and securely store the key for later use in the configuration on the tenant.

![Copy API key](https://docs.aembit.io/_astro/claude_api_copy_key.C0VuE-0R_Z1LNkV.webp)

## Server Workload Configuration

1. Create a new Server Workload.

* **Name** - Choose a user-friendly name.

2. Configure the service endpoint:

* **Host** - `api.anthropic.com`
* **Application Protocol** - HTTP
* **Port** - 443 with TLS
* **Forward to Port** - 443 with TLS
* **Authentication method** - HTTP Authentication
* **Authentication scheme** - Header
* **Header** - x-api-key

## Credential Provider Configuration

1. Create a new Credential Provider.

* **Name** - Choose a user-friendly name.
* **Credential Type** - [API Key](../../credential-providers/api-key.md)
* **API Key** - Paste the key copied from Anthropic Console.

## Client Workload Configuration

Aembit now handles the credentials required to access the Server Workload, eliminating the need for you to manage them directly. You can safely remove any previously used credentials from the Client Workload.

If you access the Server Workload through an SDK or library, it is possible that the SDK/library may still require credentials to be present for initialization purposes. In this scenario, you can provide placeholder credentials. Aembit will overwrite these placeholder credentials with the appropriate ones during the access process.

## Access Policy

* Create an Access Policy for a Client Workload to access the Claude API Server Workload. Assign the newly created Credential Provider to this Access Policy.

## Required Features

* You will need to configure the [TLS Decrypt](../../../deploy-install/advanced-options/tls-decrypt/configure-tls-decrypt.md) feature to work with the Claude API Server Workload.

> **Note**
>
> If you are using the SDK, you will need to configure the `SSL_CERT_FILE` environment variable and point it to a file containing the tenant root CA. The specific commands may vary depending on how your application is launched. Below command lines are examples for the Python SDK:
>
> ```shell
> wget https://<your_tenant_ID>.aembit.io/api/v1/root-ca -O tenant.crt
> SSL_CERT_FILE=./tenant.crt python3 ./your_app.py
> ```

## Related

**Compatible credential providers**

* [API Key](../../credential-providers/api-key.md)
