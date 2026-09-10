---
type: how-to
title: "Sauce Labs"
description: "This page describes how to configure Aembit to work with the Sauce Labs Server Workload."
resource: https://docs.aembit.io/user-guide/access-policies/server-workloads/guides/saucelabs/
interface: web-ui
tags: ["ci-cd", "guide", "server-workload", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Sauce Labs


[Sauce Labs](https://saucelabs.com/) is a comprehensive cloud-based testing platform designed to facilitate the automation and execution of web and mobile application tests. It supports a wide range of browsers, operating systems, and devices, ensuring thorough and efficient testing processes.

Below you can find the Aembit configuration required to work with the Sauce Labs as a Server Workload using the Sauce REST APIs.

> **Prerequisites**
>
> Before proceeding with the configuration, you will need to have a Sauce Labs tenant.

## Server Workload Configuration

1. Create a new Server Workload.

* **Name** - Choose a user-friendly name.

2. Configure the service endpoint:

* **Host** - Use the appropriate endpoint for your data center:

  * `api.us-west-1.saucelabs.com` for US West
  * `api.us-east-4.saucelabs.com` for US East
  * `api.eu-central-1.saucelabs.com` for Europe

* **Application Protocol** - HTTP

* **Port** - 443 with TLS

* **Forward to Port** - 443 with TLS

* **Authentication method** - HTTP Authentication

* **Authentication scheme** - Basic

## Credential Provider Configuration

1. Sign into your Sauce Labs account.

2. In the upper-right corner of any page, click the user icon and select **User Settings**.

![Sauce Labs Dashboard to User Settings](https://docs.aembit.io/_astro/saucelabs_dashbaoard_to_usersettings.D7yF_Gu7_Z19Aign.webp)

3. Under User Information, copy the **User Name**. Scroll down the page and under the Access Key section, click **Copy to clipboard** to copy the **Access Key**. Securely store both values for later use in the tenant configuration. For more information on authentication, please refer to the [official Sauce Labs documentation](https://docs.saucelabs.com/dev/api/#authentication).

![Sauce Labs Username and Access Key](https://docs.aembit.io/_astro/saucelabs_username_and_accesskey.DrSHmmVm_Z2ij3wh.webp)

4. Create a new Credential Provider.

* **Name** - Choose a user-friendly name.
* **Credential Type** - [Username & Password](../../credential-providers/username-password.md)
* **Username** - Provide the User Name copied from Sauce Labs.
* **Password** - Provide the Access Key copied from Sauce Labs.

## Client Workload Configuration

Aembit now handles the credentials required to access the Server Workload, eliminating the need for you to manage them directly. You can safely remove any previously used credentials from the Client Workload.

If you access the Server Workload through an SDK or library, it is possible that the SDK/library may still require credentials to be present for initialization purposes. In this scenario, you can provide placeholder credentials. Aembit will overwrite these placeholder credentials with the appropriate ones during the access process.

## Access Policy

* Create an Access Policy for a Client Workload to access the Sauce Labs Server Workload. Assign the newly created Credential Provider to this Access Policy.

## Required Features

* You will need to configure the [TLS Decrypt](../../../deploy-install/advanced-options/tls-decrypt/configure-tls-decrypt.md) feature to work with the Sauce Labs Server Workload.

## Related

**Compatible credential providers**

* [Username & Password](../../credential-providers/username-password.md)
