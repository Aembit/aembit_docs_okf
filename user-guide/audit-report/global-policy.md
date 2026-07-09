---
type: how-to
title: "How to review Global Policy Compliance"
description: "How to review Global Policy Compliance information in the Reporting dashboard"
resource: https://docs.aembit.io/user-guide/audit-report/global-policy/
interface: web-ui
tags: [audit-report]
timestamp: 2025-06-03T16:56:07-07:00
type_inferred: true
---

# How to review Global Policy Compliance

Global Policy Compliance is a feature in Aembit that allows you to enforce security standards across your Aembit environment. It ensures that Access Policies and Agent Controllers adhere to specific security requirements, such as Trust Provider configurations and TLS hostname settings. This helps maintain consistent security practices and prevents the creation of policies that could expose resources unintentionally.

On the Global Policy Compliance page, you can review the compliance status of your Aembit Tenant’s global policies.

## About Global Policy Compliance status

[Section titled “About Global Policy Compliance status”](#about-global-policy-compliance-status)

Aembit uses color-coded status icons and labels to indicate the compliance status of Access Policies in relation to Global Policy Compliance:

* **Red** - a required element is missing from the Access Policy.
* **Yellow** - a recommended element is missing from the Access Policy.
* **Green** - the Access Policy is compliant with Global Policy Compliance requirements.
* **Gray** - the Access Policy is disabled or not active.

When you edit an Access Policy, Aembit displays the current compliance status and prevents you from saving non-compliant Access Policies based on your configured enforcement level. This ensures that all policies meet the required security standards before they can be saved or activated.

## Reviewing Global Policy Compliance data

[Section titled “Reviewing Global Policy Compliance data”](#reviewing-global-policy-compliance-data)

To review Global Policy compliance data, perform the following steps:

1. Log into your Aembit Tenant.

2. Click **Reporting** in the left sidebar.

3. At the top, select **Reporting ☰ Global Policy Compliance**.

   Aembit displays the **Global Policy Compliance** page with a list of existing Access Policies and their **Compliance Status**.

   ![Global Policy Compliance report dashboard](https://docs.aembit.io/_astro/global-policy-compliance-report-dashboard.BybJxw5m_ZEGaSV.webp)

4. By default Aembit displays all Access Policies. You can filter the results to your liking using the following:

   * **Resource Set** - A dynamic list of Resource Sets in your Aembit Tenant. You can select a specific Resource Set to filter the Access Policies the report dashboard displays.

     Default - **All**

     Options - all Resource Sets in your Aembit Tenant.

   * **Compliance Status** - The status of the Access Policies in relation to Global Policy Compliance. You can select a specific compliance status to filter the Access Policies the report dashboard displays.

     Default - **All**

     Options - `Compliant`, `Missing Required`, `Missing Recommended`,

5. Once you have selected your filtering options, Aembit displays the Access Policies based on your filter selections in the table.
