---
type: how-to
title: "Access Condition for Wiz"
description: "This page describes how to create an Access Condition for a Wiz integration."
resource: https://docs.aembit.io/user-guide/access-policies/access-conditions/wiz/
interface: web-ui
tags: ["access-condition", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Access Condition for Wiz

## Introduction

If you have an existing Wiz integration and would like to create an Access Condition for this integration, you may create this Access Condition using your Aembit Tenant.

The section below describes the required steps to set up and configure an Access Condition for a Wiz integration.

## Creating an Access Condition for a Wiz Integration

To create an Access Condition for a Wiz integration, perform the steps listed below.

1. Log into your Aembit Tenant using your login credentials.

2. When your credentials have been authenticated and you are logged into your tenant, you are directed to the main dashboard page. Click on **Access Conditions** in the left sidebar. You will see a list of existing Access Conditions (in this example, no Access Conditions have been created)

![Access Conditions - Existing Access Conditions](https://docs.aembit.io/_astro/access_conditions_wiz_existing_access_conditions.C86pyUIw_Z1RHMDO.webp)

3. Click on the **New Access Condition** button. An Access Condition dialog window appears.

![Access Conditions Dialog Window - Empty](https://docs.aembit.io/_astro/access_conditions_wiz_dialog_window_empty.BnOCuQ6B_K6fWm.webp)

4. In the Access Condition dialog window, enter information in the following fields:

* **Name** - Name of the Access Condition.
* **Description** - An optional text description of the Access Condition.
* **Integration** - A drop-down menu that enables you to select the type of integration you would like to create. Select your existing Wiz integration from the drop-down menu.

5. In the **Conditions** section, click on the **Container Cluster Connected** toggle if you want to block Client Workloads that Wiz reports are not container cluster connected.

6. In the **Conditions - Time** section, enter the duration of time you would like to use for restricting Client Workloads in Kubernetes Clusters that have not been seen recently.

> **Note**
>
> If you would like to have a full day as the time duration, Aembit recommends using 26 hours to handle the different system synchronizations.

![Access Conditions Dialog Window - Filled Out](https://docs.aembit.io/_astro/access_conditions_wiz_dialog_window_wiz_selected_filled_out.B3fvejsF_jYGPH.webp)

7. When finished, Click **Save**. Your new Access Condition for the Wiz integration will appear on the main Access Conditions page.

![Access Conditions List With New Wiz Access Condition](https://docs.aembit.io/_astro/access_conditions_wiz_success_listed.DQ9TZzED_5VIjN.webp)
