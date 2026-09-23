---
type: explanation
title: "Roles overview"
description: "How Aembit roles, permission levels, and Resource Set assignments control what users can do in your Tenant"
resource: https://docs.aembit.io/user-guide/administration/roles/
tags: ["role", "administration"]
timestamp: 2026-09-22T13:47:38-07:00
---

# Roles overview

Roles control what users can view and change in your Aembit Tenant. Each role sets a permission level for every type of resource, and you can scope a role to one or more Resource Sets. You assign one or more roles to each user.

By creating roles and assigning permissions to that role, you can enhance your overall security profile by ensuring each role, with its assigned permissions, only has the access required.

## Permission levels

Each resource type in a role takes one of three permission levels:

| Level          | What the user can do                                               |
| -------------- | ------------------------------------------------------------------ |
| **No Access**  | Nothing. The user can’t view or change resources of this type.     |
| **Read Only**  | View resources of this type, but not create, edit, or delete them. |
| **Read/Write** | View, create, edit, and delete resources of this type.             |

Report resource types hold data that no user can change, so they offer only **No Access** and **Read Only**. These are Access Authorization Events, Global Policy Compliance Report, Audit Logs, and Workload Events.

## Default roles

Every Aembit Tenant includes the **SuperAdmin** and **Auditor** system roles. You can’t edit or delete a system role. To start a custom role from a system role’s permissions, copy them when you [add the role](add-roles.md).

## How permissions combine across roles

A user can hold more than one role. For each resource type, Aembit grants the highest permission level that any of the user’s active roles allows. A role that sets **No Access** or **Read Only** on a resource type never lowers the access that another role grants.

For example, a user holds two roles. One role sets **Read/Write** on Access Policies and the other sets **No Access** on Access Policies. The user has Read/Write access to Access Policies.

> **Restrictive roles don’t limit access**
>
> Adding a role with lower permission levels doesn’t restrict a user who also holds a more permissive role. To limit what a user can do, edit the role that grants the access, or remove that role from the user.

When your Tenant uses Resource Sets, Aembit combines permissions per Resource Set. A role grants its permissions only within the Resource Sets assigned to it. Aembit ignores inactive roles when it evaluates a user’s permissions.

The following pages provide more information about managing roles in your Aembit Tenant:

* [Adding Roles](add-roles.md) - Learn how to add roles to your Aembit Tenant
