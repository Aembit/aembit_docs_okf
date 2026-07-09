---
type: how-to
title: "Aembit Time Condition"
description: "This page describes how to create an Access Condition for a specific Time Condition."
resource: https://docs.aembit.io/user-guide/access-policies/access-conditions/aembit-time-condition/
interface: web-ui
tags: [access-condition, access-policy]
timestamp: 2025-05-22T22:55:26-07:00
type_inferred: true
---

# Aembit Time Condition

## Introduction

[Section titled “Introduction”](#introduction)

One type of Access Condition you may create in your Aembit Tenant is a Time Condition. This is especially useful if you would like to only grant access to Client Workloads during specific periods of time (days/hours).

The section below describes the required steps to setup and configure a Time Condition Access Condition.

## Creating a Time Condition Access Condition

[Section titled “Creating a Time Condition Access Condition”](#creating-a-time-condition-access-condition)

To create a Time Condition Access Condition, perform the steps below.

1. Log into your Aembit Tenant using your login credentials.

2. When your credentials have been authenticated and you are logged into your tenant, you are directed to the main dashboard page. Click on **Access Conditions** in the left sidebar. You will see a list of existing Access Conditions (in this example, no Access Conditions have been created)

![Access Conditions List - Blank](https://docs.aembit.io/_astro/access_conditions_blank.Dr-PNxRw_ZtpIbO.webp)

3. Click on the **New Access Condition** button. An Access Condition dialog window appears.

![Access Condition Dialog Window - Empty](https://docs.aembit.io/_astro/access-condition-time-condition-dialog-window.DNrkqmcQ_1hXKu9.webp)

4. In the Access Condition dialog window, enter information in the following fields:

* **Name** - Name of the Access Condition.
* **Description** - An optional text description of the Access Condition.
* **Integration** - A drop-down menu that enables you to select the type of integration you would like to create. Select **Aembit Time Condition** from the drop-down menu.

![Access Condition Dialog Window - Time Condition Selected](https://docs.aembit.io/_astro/access-condition-time-condition-integration-selected.DjCmUhIk_Z1xzsHN.webp)

5. In the Conditions section, click on the **Timezone** drop-down menu to select the timezone you would like to use for your Access Condition.

6. Click on the **+** icon next to each day you would like to use in your Time Condition configuration.

Note

At least one time condition is required.

![Access Condition Dialog Window - Time Condition Completed](https://docs.aembit.io/_astro/access-condition-dialog-window-time-condition-completed.cbh53B2M_POv0j.webp)

7. Click **Save**. Your new Aembit Time Condition Access Condition now appears on the main Access Conditions page.

![Access Condition Main Page - Time Condition Listed](https://docs.aembit.io/_astro/access-condition-main-page-new-time-condition.DIchorwX_Z1bNkOR.webp)
