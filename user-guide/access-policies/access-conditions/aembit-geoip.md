---
type: how-to
title: "Access Conditions for GeoIP Restriction"
description: "This document provides a description on how to setup and configure an Access Condition for a GeoIP Restriction."
resource: https://docs.aembit.io/user-guide/access-policies/access-conditions/aembit-geoip/
interface: web-ui
tags: ["access-condition", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Access Conditions for GeoIP Restriction


You may configure an Access Condition to enable GeoIP restrictions. This can be useful if you would like to only grant access to Client Workloads from specific locations. A GeoIP restriction ensures any request received from a locale that is not already specified will be blocked. For example, if you would like to allow requests from a specific country or region, you may simply add an Access Condition for that region or area.

## Creating a GeoIP Access Condition

To create a GeoIP Restriction Access Condition, perform the steps listed below.

1. Log into your Aembit Tenant using your login credentials.

2. When your credentials have been authenticated and you are logged into your tenant, you are directed to the main dashboard page. Click on **Access Conditions** in the left sidebar. You will see a list of existing Access Conditions.

![Access Conditions List](https://docs.aembit.io/_astro/access-conditions-existing-list.DHZC7PDi_2mPHu2.webp)

3. Click on the **New Access Condition** button. An Access Condition dialog window appears.

![Access Condition Dialog Window - Empty](https://docs.aembit.io/_astro/access-conditions-empty-dialog-window.hnszIuhT_Z223bGX.webp)

4. In the Access Condition dialog window, enter information in the following fields:

* **Name** - Name of the Access Condition.
* **Description** - An optional text description of the Access Condition.
* **Integration** - A drop-down menu that enables you to select the type of integration you would like to create. Select **Aembit GeoIP Condition** from the drop-down menu.

![Access Condition Dialog Window - GeoIP Selected](https://docs.aembit.io/_astro/access-conditions-geoip-selected.CyXXOkj7_24MoXF.webp)

5. In the Conditions -> Location section, click on the **Country** drop-down menu to select the country you would like to use for your Access Condition.

6. After selecting a **Country** from the drop-down menu, you will see an expanded drop-down menu where you may select a **Subdivision** you want to use for that country. A Subdivision may be a region, state, province, or other territory that you would like to use for further Access Condition scoping.

![Access Condition Dialog Window - Country and Subdivision Selected](https://docs.aembit.io/_astro/access-conditions-geoip-country-selected.D19l2QD2_ZEU6b4.webp)

> **Note**
>
> You may select more than one Subdivision for a country by clicking on the **+** icon.

7. Click **Save**. Your new Aembit GeoIP Access Condition now appears on the main Access Conditions page.

![Access Conditions List With GeoIP Listed](https://docs.aembit.io/_astro/access-conditions-list-with-geoip.CcabgKA1_ZLHlvW.webp)

## GeoIP Accuracy Limitations and Best Practices for Cloud Data Centers

When configuring GeoIP-based access conditions, it is important to know the limitations in geolocation accuracy, especially for workloads hosted in cloud data centers such as AWS, Azure, Google Cloud, and others. Due to the dynamic and shared nature of cloud infrastructure, geolocation services often provide lower confidence levels for specific subdivisions (e.g., states, provinces) or cities for cloud-based IP addresses.

As a result, Aembit recommends customers limit GeoIP conditions to the country level for workloads in cloud data centers. This approach ensures more reliable geolocation data while still providing geographic-based access control. Using subdivisions or cities for cloud-hosted workloads can result in access failures if the geolocation confidence falls below acceptable thresholds.
