---
type: how-to
title: "How to delete a Resource Set"
description: "How to delete a custom Resource Set and the cascading deletion of all entities it contains."
resource: https://docs.aembit.io/user-guide/administration/resource-sets/delete-resource-set/
interface: web-ui
tags: [resource-set, administration]
timestamp: 2026-06-29T15:52:52-04:00
type_inferred: true
---

# How to delete a Resource Set

Deleting a Resource Set permanently removes the Resource Set and **every entity it contains**.

You can only delete Resource Sets that an administrator created. Aembit protects the Default Resource Set, so you can’t delete it.

Deletion is a destructive, irreversible action, so Aembit requires you to confirm it.

Deleting a Resource Set deletes everything inside it

Deleting a Resource Set also deletes all entities associated with it. Any Agent Proxy or Edge components configured to use the Resource Set stop functioning after the operation. This action cannot be undone.

## Before you begin

[Section titled “Before you begin”](#before-you-begin)

You need **Write access** (or an administrator role) to the Resource Set you want to delete. A user without Write or administrator access can’t delete a Resource Set.

You **can’t delete the Default Resource Set**. Aembit doesn’t display a **Delete** action for it.

## Delete a Resource Set

[Section titled “Delete a Resource Set”](#delete-a-resource-set)

You can delete a custom Resource Set from the **Resource Sets** list or from the Resource Set’s details panel.

1. Log into your Aembit Tenant.

2. Click **Administration** in the left sidebar.

3. At the top, select **Administration ☰ Resource Sets**.

   Aembit displays the **Resource Sets** page with a list of existing Resource Sets.

4. Start the deletion in one of two ways:

   * In the row for the custom Resource Set you want to delete, click the actions menu (**⋯**) at the end of the row, then select **Delete**.
   * Or, select the custom Resource Set to open its details panel, then click **Delete**.

   Aembit displays the **Delete Resource Set** confirmation dialog, which lists everything that will be deleted.

5. Review the warning, then click **Delete** to confirm, or **Cancel** to keep the Resource Set.

## What gets deleted

[Section titled “What gets deleted”](#what-gets-deleted)

When you delete a Resource Set, Aembit deletes the Resource Set and all entities associated with it:

* Access Policies
* Client Workloads
* Trust Providers
* Access Conditions and Integrations
* Credential Providers and Integrations
* Server Workloads
* Standalone CAs
* Routings

## What to expect after deletion

[Section titled “What to expect after deletion”](#what-to-expect-after-deletion)

* **The action can’t be undone.** Once you confirm, you can’t recover the Resource Set or its entities.
* **Agent Proxy and Edge components stop working.** Any Agent Proxy or Edge component configured to use the deleted Resource Set no longer functions after the operation.
* **Managed-account integrations are decommissioned.** If the Resource Set contains a managed-account integration (such as a [Managed GitLab Account](../../access-policies/credential-providers/managed-gitlab-account.md)), Aembit decommissions the integration and deletes the associated managed account.
* **The Resource Set leaves Reporting.** The Resource Set is no longer available within the **Reporting** section, including [Global Policy Compliance](../../audit-report/global-policy.md).
* **Log Streams finish in flight.** Existing Log Streams continue sending Resource Set-associated data until they complete.
* **No data is left behind.** Aembit removes the Resource Set and all its associated entities, leaving no orphaned data in the UI or API.
* **The deletion is all-or-nothing.** If an error occurs partway through, the operation fails and rolls back, and the Resource Set and its contents remain intact.
* **Aembit records audit entries.** Aembit logs an audit entry for each entity deleted within the Resource Set, plus an entry for the Resource Set deletion itself within the Default Resource Set. You can review these through Log Streams and [Audit Logs](../../audit-report/audit-logs.md).

## Related

[Section titled “Related”](#related)

* [Resource Sets overview](overview.md)
* [Assign roles](assign-roles.md)
* [Audit Logs](../../audit-report/audit-logs.md)
