---
type: how-to
title: "Create a Wiz Discovery Integration"
description: "How to create a Wiz Discovery Integration"
resource: https://docs.aembit.io/user-guide/administration/discovery/integrations/wiz/
interface: web-ui
tags: [integration, discovery, administration]
timestamp: 2025-05-29T11:26:12-07:00
type_inferred: true
---

# Create a Wiz Discovery Integration

Note

This is a beta feature and may be subject to changes.

This page describes how to create a new Wiz integration for [Discovery](../../../discovery/overview.md).

## Prerequisites

[Section titled “Prerequisites”](#prerequisites)

Before you begin, you must have access to the following:

* **Wiz Account** - You should have a **Wiz account**.

## Set up a service account in Wiz

[Section titled “Set up a service account in Wiz”](#set-up-a-service-account-in-wiz)

1. Sign in to your **Wiz account**.

2. Go to **Settings -> Integrations**.

3. Click **+ Add Integration** in the top-right corner.

![Adding Integration on Wiz](https://docs.aembit.io/_astro/discovery_wiz_add_integration.DQipfYxC_Z1hkSoT.webp)

1. Search for **Aembit** and click the **Aembit integration**.

![Wiz - Searching for Aembit Integration](https://docs.aembit.io/_astro/discovery_wiz_search_aembit.adIf_mGF_1EsDeJ.webp)

1. Provide a name for your integration (for example, **Aembit Discovery integration**).

2. Click **Add integration** at the bottom bar.

![Complete Integration](https://docs.aembit.io/_astro/discovery_new_aembit_integration.DhJs612J_ZQki7r.webp)

1. Open a new browser window or **copy** the following details from Wiz, as you’ll need them in the [next section](#configure-wiz-discovery):

   * **API Endpoint URL**
   * **Token URL**
   * **Client ID**
   * **Client Secret**

   ![Wiz Integration Details](https://docs.aembit.io/_astro/discovery_wiz_integration_details.T7C2vXIe_Z2unSaX.webp)

## Configure Wiz Discovery

[Section titled “Configure Wiz Discovery”](#configure-wiz-discovery)

Follow these steps to configure the Wiz integration in your Aembit Tenant:

1. Log into your Aembit Tenant.

2. Click **Administration** in the left sidebar.

3. At the top, select **Administration ☰ Discovery**

4. Click **+ New**.

5. Select **Wiz integration** from the available options.

6. Using the details from the final step in the [previous section](#set-up-a-service-account-in-wiz), fill in the integration details:

   * **Name** - The name of the Integration. For example, **Wiz Discovery**.
   * **Description** - An optional text description for the Integration.
   * **Endpoint** - Paste the **API Endpoint URL** you copied earlier.
   * **Sync Frequency** - Choose the sync frequency from dropdown menu.
   * **OAuth Token Endpoint** - Paste the **Token URL** from the previous step.
   * **Client ID** - Paste the **Client ID** you copied earlier.
   * **Client Secret** - Paste the **Client Secret** you copied earlier.
   * **Audience** - Enter `wiz-api`.

   ![Wiz Integration Configuration on Aembit](https://docs.aembit.io/_astro/discovery_aembit_new_integration.ComwdqfZ_1wJFGi.webp)

7. Click **Save**.
