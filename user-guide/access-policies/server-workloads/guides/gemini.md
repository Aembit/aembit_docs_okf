---
type: how-to
title: "Gemini (Google)"
description: "This page describes how to configure Aembit to work with the Gemini Server Workload"
resource: https://docs.aembit.io/user-guide/access-policies/server-workloads/guides/gemini/
interface: web-ui
tags: [ai-ml, guide, server-workload, access-policy]
timestamp: 2025-05-29T11:26:12-07:00
type_inferred: true
---

# Gemini (Google)


[Gemini](https://ai.google.dev/) is an AI platform that allows developers to integrate multimodal capabilities into their applications, including text, images, audio, and video processing. It supports tasks such as natural language processing, content generation, and data analysis.

Below you can find the Aembit configuration required to work with the Google Gemini service as a Server Workload using the REST API.

## Prerequisites

[Section titled “Prerequisites”](#prerequisites)

Before proceeding with the configuration, ensure you have a Google account and an API key. If you have not already created a key, follow the instructions below. For more details about the Gemini API, refer to the [official Gemini API documentation](https://ai.google.dev/gemini-api/docs/api-key).

### Create API Key

[Section titled “Create API Key”](#create-api-key)

1. Navigate to the [API Keys](https://aistudio.google.com/app/apikey) page and sign in to your Google account.

2. Click the **Create API key** button in the middle of the page.

![Google AI Studio | Get API Keys](https://docs.aembit.io/_astro/gemini_get_api_key.5aFdiUT5_1PeHOS.webp)

3. Click the **Got it** button on the Safety Setting Reminder pop-up window.

4. If you do not already have a project in Google Cloud, click **Create API key in new project**. Otherwise, select from your projects and click **Create API key in existing project**.

![Create API key](https://docs.aembit.io/_astro/gemini_create_api_key.6ojSpf4H_MyvcN.webp)

5. Click **Copy** and securely store the key for later use in your tenant configuration.

![Copy API key](https://docs.aembit.io/_astro/gemini_copy_api_key.o3dM7V3B_SuzU9.webp)

## Server Workload Configuration

[Section titled “Server Workload Configuration”](#server-workload-configuration)

1. Create a new Server Workload.

* **Name** - Choose a user-friendly name.

2. Configure the service endpoint:

* **Host** - `generativelanguage.googleapis.com`
* **Application Protocol** - HTTP
* **Port** - 443 with TLS
* **Forward to Port** - 443 with TLS
* **Authentication method** - HTTP Authentication
* **Authentication scheme** - Header
* **Header** - x-goog-api-key

## Credential Provider Configuration

[Section titled “Credential Provider Configuration”](#credential-provider-configuration)

1. Create a new Credential Provider.

* **Name** - Choose a user-friendly name.
* **Credential Type** - [API Key](../../credential-providers/api-key.md)
* **API Key** - Paste the key copied from Google AI Studio.

## Client Workload Configuration

[Section titled “Client Workload Configuration”](#client-workload-configuration)

Aembit now handles the credentials required to access the Server Workload, eliminating the need for you to manage them directly. You can safely remove any previously used credentials from the Client Workload.

If you access the Server Workload through an SDK or library, it is possible that the SDK/library may still require credentials to be present for initialization purposes. In this scenario, you can provide placeholder credentials. Aembit will overwrite these placeholder credentials with the appropriate ones during the access process.

## Access Policy

[Section titled “Access Policy”](#access-policy)

* Create an Access Policy for a Client Workload to access the Gemini Server Workload. Assign the newly created Credential Provider to this Access Policy.

## Required Features

[Section titled “Required Features”](#required-features)

* You will need to configure the [TLS Decrypt](../../../deploy-install/advanced-options/tls-decrypt/configure-tls-decrypt.md) feature to work with the Gemini Server Workload.

## Related

**Compatible credential providers**

* [API Key](../../credential-providers/api-key.md)
