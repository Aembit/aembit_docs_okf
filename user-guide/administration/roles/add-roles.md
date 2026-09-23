---
type: how-to
title: "How to add a new role"
description: "How to create a new Role in your Aembit Tenant"
resource: https://docs.aembit.io/user-guide/administration/roles/add-roles/
interface: web-ui
tags: ["role", "administration"]
timestamp: 2026-09-22T13:47:38-07:00
---

# How to add a new role

To add a role to your Aembit Tenant, perform the following steps. Before you add a role that restricts access, read [How permissions combine across roles](overview.md#how-permissions-combine-across-roles), because a restrictive role doesn’t limit a user who also holds a permissive one.

1. Log into your Aembit Tenant.

2. Click **Administration** in the left sidebar.

3. At the top, select **Administration ☰ Roles**.

   Aembit displays the **Roles** page with a list of existing roles.

   ![Roles Page](https://docs.aembit.io/_astro/administration_roles_main_page.CTdXHAh8_ZoSmaJ.webp)

   > **Note**
   >
   > By default, Aembit Tenants include the **SuperAdmin** and **Auditor** system roles. You can’t edit or delete a system role.

4. Click **+ New**, revealing the **Roles** pop out menu.

   ![Roles Dialog Window - Empty](https://docs.aembit.io/_astro/administration_roles_add_new_role_dialog_window.BzkyKtSX_ZqDxiR.webp)

5. Fill out the following fields:

   * **Name** - The name of the Role. Role names must be unique in your Tenant.
   * **Description** - An optional text description of the Role.
   * **Resource Set Assignments** - A drop-down menu that assigns existing Resource Sets to the Role. This field appears when your Tenant uses Resource Sets. A user whose Roles have no Resource Set may not be able to sign in to Aembit.
   * **Permissions** - For each resource type, select **No Access**, **Read Only**, or **Read/Write**. To start from an existing Role’s permissions, select that Role in **Copy from existing Role…**. Aembit fills the radio buttons with that Role’s permissions, and you can then adjust them. This option appears only when you create a Role. See [Permission levels](overview.md#permission-levels) for what each level allows.

   In the following example, the new Role copies the **SuperAdmin** permissions:

   ![Roles Dialog Window - Completed](https://docs.aembit.io/_astro/administration_roles_dialog_window_completed.B4athzIY_YYP56.webp)

6. Click **Save**.

   Aembit displays the role on the **Roles** page.

   ![Roles Page - New Role Added](https://docs.aembit.io/_astro/administration_roles_main_page_with_new_role.DJaA2L8K_ZUccTm.webp)
