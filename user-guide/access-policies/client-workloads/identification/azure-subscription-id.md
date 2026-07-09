---
type: how-to
title: "Azure Subscription ID"
description: "How to identify Azure workloads using the Azure Subscription ID within Aembit"
resource: https://docs.aembit.io/user-guide/access-policies/client-workloads/identification/azure-subscription-id/
interface: web-ui
tags: [azure, identification, client-workload, access-policy]
timestamp: 2025-06-06T13:01:11-04:00
type_inferred: true
---

# Azure Subscription ID

This page explains how to use the **Azure Subscription ID** identifier to uniquely identify workloads deployed on **Azure**.

## Understanding the Azure Subscription ID identifier

[Section titled “Understanding the Azure Subscription ID identifier”](#understanding-the-azure-subscription-id-identifier)

When you deploy applications to Azure, you use the Subscription ID to isolate and group resources by ownership or environment. Each Azure Subscription owns the resources associated with it. For more info, see [“Get subscription and tenant IDs in the Azure portal”](https://learn.microsoft.com/en-us/azure/azure-portal/get-subscription-tenant-id) in the Microsoft docs.

## Applicable deployment type

[Section titled “Applicable deployment type”](#applicable-deployment-type)

Aembit supports the Azure Subscription ID identification method for Edge-based deployments on [Virtual Machines](../../../deploy-install/virtual-machine/overview.md) deployed to Azure.

## Create a Client Workload with an Azure Subscription ID identifier

[Section titled “Create a Client Workload with an Azure Subscription ID identifier”](#create-a-client-workload-with-an-azure-subscription-id-identifier)

To configure a Client Workload with an Azure Subscription ID identifier, follow these steps:

1. Log into your Aembit Tenant.

2. Click **Client Workloads** in the left nav pane.

3. Click **New**, revealing the **Client Workload** pop out menu.

4. Enter the **Name** and optional **Description** for the Client Workload.

5. Under **Client Identification**, select **Azure Subscription ID**.

   For **Value**, enter the Azure Subscription ID where the workload is running.

   For example, if your Azure Subscription ID is `11111111-2222-3333-4444-555555555555`, enter that in the **Value** field.

   If you don’t know the Azure Subscription ID or how to find it, see [Find Azure Subscription ID](#find-azure-subscription-id).

6. Click **Save**.

   Aembit displays the new Client Workload on the **Client Workloads** page.

Client Workload identifier uniqueness

When you identify a Client Workload using a single identifier such as this one in a complex environment, this identifier may not always provide sufficient uniqueness.

To avoid Aembit unintentionally matching something beyond what it’s intended to match, Aembit recommends that you set additional identifiers to compliment this one, creating a Client Workload identity that’s unique across your environment.

See [Using multiple Client Workload identifiers](client-workload-multiple-ids.md) for guidance on combining this identifier with more specific identifiers such as those on the [Client Workload Identifiers overview](overview.md) page, which includes vendor- and technology-specific identifiers.

## Find Azure Subscription ID

[Section titled “Find Azure Subscription ID”](#find-azure-subscription-id)

To find your Azure Subscription ID in the Azure Portal, follow these steps:

Note

You can also find the Azure Subscription ID in the Subscriptions blade or by using the Azure CLI.

Be sure to enter the full GUID format without spaces.

1. Go to the [Azure Portal](https://portal.azure.com/).

2. Use the search bar to search for **Subscriptions**.

3. You can find the **Subscription ID** listed in the **Subscriptions** table.

   Use this value in your Aembit configuration.

## Related

**Compatible trust providers**

* [Azure Instance Metadata Service](../../trust-providers/azure-metadata-service-trust-provider.md)
