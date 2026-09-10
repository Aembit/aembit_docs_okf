---
type: how-to
title: "Azure VM ID"
description: "How to identify Azure workloads using the Azure VM ID within Aembit"
resource: https://docs.aembit.io/user-guide/access-policies/client-workloads/identification/azure-vm-id/
interface: web-ui
tags: ["azure", "identification", "client-workload", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Azure VM ID

This page explains how to use the **Azure VM ID** identifier to uniquely identify workloads deployed on **Azure**.

## Understanding the Azure VM ID identifier

When you deploy applications to Azure, you often identify specific virtual machine instances by their VM IDs. Azure assigns each virtual machine a unique identifier at creation. For more details, see the [“Understand names and instance IDs for Azure Virtual Machine Scale Set VMs](https://learn.microsoft.com/en-us/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-instance-ids) in the Microsoft docs.

## Applicable deployment type

Aembit supports the Azure VM ID identification method for Edge-based deployments on [Virtual Machines](../../../deploy-install/virtual-machine/overview.md) deployed to Azure.

## Create a Client Workload with an Azure VM ID identifier

To configure a Client Workload with an Azure VM ID identifier, follow these steps:

1. Log into your Aembit Tenant.

2. Click **Client Workloads** in the left nav pane.

3. Click **New**, revealing the **Client Workload** pop out menu.

4. Enter the **Name** and optional **Description** for the Client Workload.

5. Under **Client Identification**, select **Azure VM ID**.

   For **Value**, enter the VM ID where the workload is running.

   For example, if your Azure VM ID is `12345678-1234-1234-1234-123456789abc`, enter that in the **Value** field.

   If you don’t know the Azure VM ID or how to find it, see [Find Azure VM ID](#find-azure-vm-id).

6. Click **Save**.

   Aembit displays the new Client Workload on the **Client Workloads** page.

## Find Azure VM ID

Locate your Azure VM’s Resource group and VM name using either of the following methods:

### Azure Portal

1. Go to the [Azure Portal](https://portal.azure.com/).

2. From the left menu or search bar, choose or search for **Virtual Machines**, then select your VM.

3. Copy the **Computer name** and **Resource group** from the Properties tab of the VM details page.

   Use these values in your Aembit configuration.

### Azure CLI

1. Open your terminal or command prompt.

2. Use the following command to get the VM ID:

   ```plaintext
   az vm show --resource-group <your-resource-group> --name <your-vm-name> --query vmId --output tsv
   ```

   Use these values in your Aembit configuration.

## Related

**Compatible trust providers**

* [Azure Instance Metadata Service](../../trust-providers/azure-metadata-service-trust-provider.md)
