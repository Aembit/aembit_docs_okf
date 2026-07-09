---
type: how-to
title: "Create Access Conditions for CrowdStrike"
description: "How to create an Access Condition for a CrowdStrike integration"
resource: https://docs.aembit.io/user-guide/access-policies/access-conditions/crowdstrike/
interface: web-ui
tags: [access-condition, access-policy]
timestamp: 2025-08-19T16:35:12-07:00
type_inferred: true
---

# Create Access Conditions for CrowdStrike

CrowdStrike Access Conditions enable you to restrict access to Client Workloads based on the CrowdStrike Agent’s reported state. This includes conditions such as whether the Agent is in Reduced Functionality Mode, whether the Hostname matches the expected value, or whether the Serial Number matches the expected value.

You must have an existing [CrowdStrike Integration](integrations/crowdstrike.md) to create an Access Condition for CrowdStrike.

To create an Access Condition for a CrowdStrike integration, follow these steps:

1. Log into your Aembit Tenant.

2. Go to **Access Conditions** in the left sidebar.

3. Click **+ New**, revealing the **Access Condition** pop out menu.

4. Enter a **Name** and optional **Description** for the Access Condition.

5. In the **Integration** section, select the CrowdStrike integration you want to use for this Access Condition.

   If you don’t have an existing CrowdStrike integration, you must create one first. See [CrowdStrike Integration](integrations/crowdstrike.md) for more info.

6. In the **Conditions** section, toggle the Access Conditions you would like Aembit to use to restrict access to Client Workloads in your CrowdStrike environment.

   You can pick from the following options:

   * **Restrict Reduced Functionality Mode** - This toggle ensures the CrowdStrike Agent reports if the Crowdstrike Agent on the Host isn’t in Reduced Functionality Mode.
   * **Hostname** - This toggle ensures the CrowdStrike Agent reported HostName matches the Aembit Agent Proxy retrieved HostName.
   * **Serial Number** - This toggle ensures the CrowdStrike Agent Host Serial Number matches the Aembit Agent Proxy retrieved Host Serial Number.
   * **MAC Address** - This toggle ensures the CrowdStrike Agent Host MAC Address matches the Aembit Agent Proxy retrieved Host MAC Address.
   * **Local IP Address** - This toggle ensures the CrowdStrike Agent Host Local IP Address matches the Aembit Agent Proxy retrieved Host Local IP Address.

7. In the **Time** section, enter the number of `hours`, `days`, or `weeks` that you would like to use to restrict Client Workloads that were **Last Seen** before the specified time span. For example, if you enter `2` `hours`, Aembit restricts access to Client Workloads that were last seen more than 2 hours ago.

   Once complete, the form should look similar to the following:

   ![Access Condition Dialog Window - CrowdStrike Selected](https://docs.aembit.io/_astro/access-condition-crowdstrike-form-complete.Dxckaop-_Z1CruYD.webp)

8. Click **Save**.

   Aembit displays the new Access Condition for the CrowdStrike integration in the list of Access Conditions.
