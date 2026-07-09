---
type: how-to
title: "Managing Global Policy Compliance"
description: "How to configure Aembit's Global Policy Compliance"
resource: https://docs.aembit.io/user-guide/administration/global-policy/manage-global-policy/
interface: web-ui
tags: [global-policy, administration]
timestamp: 2025-06-03T16:56:07-07:00
type_inferred: true
---

# Managing Global Policy Compliance

This topic details how you can manage Global Policy Compliance in your Aembit Tenant.

## Permission requirements

[Section titled “Permission requirements”](#permission-requirements)

To configure Global Policy Compliance settings, your users must have the **Global Policy Compliance** permission with write access. You can set this permission in the [Users page](../users/overview.md) to any of the following:

* **No Access** - Can’t view or modify settings
* **Read-Only** - Can view settings but not modify them
* **Read/Write** - Can view and modify settings

## Configure Global Policy Compliance settings

[Section titled “Configure Global Policy Compliance settings”](#configure-global-policy-compliance-settings)

1. Log into your Aembit Tenant.

2. Go to **Administration** in the left sidebar menu.

3. At the top, select **Administration ☰ Global Policy Compliance**.

   Aembit displays the following options:

   ![Aembit Administration - Global Policy Compliance screen](https://docs.aembit.io/_astro/global-policy-settings.DrFjcm5S_Z259oOl.webp)

The Global Policy Compliance page contains the settings that you can enforce specific security controls. For each setting, you can select from the following enforcement levels:

* **Required** - Prevents creation/modification of non-compliant policies
* **Recommended** - Displays warnings but allows creation after confirmation
* **Optional** - No enforcement applied

### Access Policy settings

[Section titled “Access Policy settings”](#access-policy-settings)

You can configure the following Access Policy enforcement levels:

* **Trust Provider Requirement** - Set to Required, Recommended, or Optional
* **Access Condition Requirement** - Set to Required, Recommended, or Optional

### Agent Controller settings

[Section titled “Agent Controller settings”](#agent-controller-settings)

You can configure the following Agent Controller enforcement levels:

* **Trust Provider Requirement** - Set to Required, Recommended, or Optional
* **TLS Hostname Requirement** - Set to Required, Recommended, or Optional

## Identify non-compliant Access Policies

[Section titled “Identify non-compliant Access Policies”](#identify-non-compliant-access-policies)

After configuring your [Global Policy Compliance settings](#configure-global-policy-compliance-settings):

1. Go to **Access Policies** in the left sidebar menu to view compliance status.
2. Look for the [color-coded status icons](overview.md#global-policy-compliance-status-icons) in the first column. The status icons indicate whether an Access Policy is compliant with your compliance policy settings.
3. Hover over icons to view specific compliance information or select an Access Policy to see more details about it.

Alternatively, you can review the compliance status of all Access Policies in your Aembit Tenant through the [Global Policy Compliance report dashboard](../../audit-report/global-policy.md).

## Edit non-compliant Access Policies

[Section titled “Edit non-compliant Access Policies”](#edit-non-compliant-access-policies)

When editing Access Policies under Global Policy Compliance:

1. Log into your Aembit Tenant and go to **Access Policies** in the left sidebar menu.

2. Select the Access Policy you want to view.

3. In the **Notes** section, Aembit displays **Compliance** information.

4. When saving a policy:

   * If missing required elements, you can’t save until addressed
   * If missing recommended elements, you’re prompted with a confirmation dialog

   Aembit prevents you from saving your changes when you haven’t configured the elements your compliance policy *requires*.

   For *recommended* elements that you haven’t configured, Aembit warns you that saving the policy as-is isn’t recommended.

5. To save your Access Policy, you must have no required elements not configured.
