---
type: how-to
title: "Freshsales"
description: "This page describes how to configure Aembit to work with the Freshsales Server Workload."
resource: https://docs.aembit.io/user-guide/access-policies/server-workloads/guides/freshsales/
interface: web-ui
tags: ["productivity", "guide", "server-workload", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Freshsales


[Freshsales](https://www.freshworks.com/crm/sales/) is a customer relationship management platform that helps businesses manage their sales processes. It offers features like lead tracking, email integration, and sales analytics to streamline workflows and improve customer interactions.

Below you can find the Aembit configuration required to work with the Freshsales service as a Server Workload using the REST API.

## Prerequisites

Before proceeding with the configuration, you will need to have a Freshsales or Freshsales Suite tenant (or [sign up](https://www.freshworks.com/crm/signup/) for one).

## Server Workload Configuration

1. Create a new Server Workload.

* **Name** - Choose a user-friendly name.

2. Configure the service endpoint:

* **Host** - `<domain>.myfreshworks.com`
* **Application Protocol** - HTTP
* **Port** - 443 with TLS
* **Forward to Port** - 443 with TLS
* **Authentication method** - HTTP Authentication
* **Authentication scheme** - Header
* **Header** - Authorization

## Credential Provider Configuration

1. Sign into your Freshsales account.

2. In the upper-right corner of the page, click your profile photo, then click **Settings**.

![Freshsales Dashboard](https://docs.aembit.io/_astro/freshsales_dashboard.BenUvDiZ_e8fbT.webp)

3. Click on the **API Settings** tab.

4. Click **Copy** and securely store the API key for later use in the configuration on the tenant.

![Copy Freshsales CRM API Key](https://docs.aembit.io/_astro/freshsales_settings_api_key.CgfwRntw_26VOdY.webp)

5. Create a new Credential Provider.

* **Name** - Choose a user-friendly name.
* **Credential Type** - [API Key](../../credential-providers/api-key.md)
* **API Key** - Provide the key copied from Freshsales.

## Client Workload Configuration

Aembit now handles the credentials required to access the Server Workload, eliminating the need for you to manage them directly. You can safely remove any previously used credentials from the Client Workload.

If you access the Server Workload through an SDK or library, it is possible that the SDK/library may still require credentials to be present for initialization purposes. In this scenario, you can provide placeholder credentials. Aembit will overwrite these placeholder credentials with the appropriate ones during the access process.

## Access Policy

* Create an access policy for a Client Workload to access the Freshsales Server Workload and assign the newly created Credential Provider to it.

## Required Features

* You will need to configure the [TLS Decrypt](../../../deploy-install/advanced-options/tls-decrypt/configure-tls-decrypt.md) feature to work with the Freshsales Server Workload.

## Related

**Compatible credential providers**

* [API Key](../../credential-providers/api-key.md)
