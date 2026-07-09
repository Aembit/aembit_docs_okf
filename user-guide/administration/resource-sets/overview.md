---
type: explanation
title: "Resource Sets overview"
description: "Description of what Resource Sets are and how they work"
resource: https://docs.aembit.io/user-guide/administration/resource-sets/
tags: [resource-set, administration]
timestamp: 2026-06-23T16:24:12-07:00
type_inferred: true
---

# Resource Sets overview

In complex environments, managing access to sensitive resources requires granular control. Aembit’s Resource Sets are an advanced feature that extends Aembit’s existing Role-Based Access Control (RBAC) capabilities, providing fine-grained permissions and roles within your Aembit Tenant. This feature enables you to define and manage logical and isolated sets of resources. Resources include things such as Client Workloads, Server Workloads, deployed Agent Proxy instances and their associated operational events such as Audit Logs, Access Authorization, and Workload Events.

Each Resource Set acts as a mini-environment or sub-tenant, enabling segmentation of security boundaries to best secure your environment. This segmentation allows roles to be specifically tailored for your Resource Sets, thereby ensuring that users and workloads have access limited to the resources necessary for their designated tasks. Therefore, this approach not only enhances security by adhering to the principle of least privilege (PoLP) but also supports complex operational and organizational configurations.

### Configuration

[Section titled “Configuration”](#configuration)

Resource Sets primarily govern Access Policies and their associated entities. The following list contains all available Access Policy entities:

* Client Workloads

* Trust Providers

* Access Conditions

* Integrations

* Credential Providers

* Server Workloads

The resources you configure can then operate independently of similar or identical resources in other Resource Sets, enabling numerous configuration and control options.

To ensure this separation, Aembit administrators can configure user assigned roles associated to specific Resource Sets and assign users to these roles. This logical association enables support for numerous advanced permission sets as best suited for your organization’s security needs.

Aembit generates Audit Logs for all configuration updates, separates them out into their respective Resource Sets, and ensures they’re only visible to those users with the appropriate permissions.

### Deployment

[Section titled “Deployment”](#deployment)

You can specify a Resource Set association when deploying an Aembit Agent Proxy or using the Aembit Agent. This enables all operational activity to execute within the bounds of that Resource Set.

### Reporting

[Section titled “Reporting”](#reporting)

Aembit segments its comprehensive event logging, which includes Audit Logs, Access Authorization, and Workload Events, into the associated Resource Set. Aembit restricts access to these events only to authorized users. This separation ensures that event data is logically isolated but also subject to stringent access controls, restricting visibility to authorized users within each specific Resource Set.

Resource Sets empower you to enforce the principle of least privilege. PoLP makes sure that your users can only view configuration details and operational results for the environments and workloads under their direct responsibility. Moreover, this approach facilitates compliance by providing clear audit trails within defined security boundaries, and it simplifies troubleshooting by limiting the scope of event analysis to relevant resource contexts.

### Deleting Resource Sets

[Section titled “Deleting Resource Sets”](#deleting-resource-sets)

You can delete a custom Resource Set when you no longer need it. Deleting a Resource Set also deletes every entity it contains and cannot be undone, so Aembit restricts deletion to authorized users and requires confirmation. The Default Resource Set cannot be deleted or modified. See [Delete a Resource Set](delete-resource-set.md) for the full workflow and its effects.

## About Resource Set Roles and Permissions

[Section titled “About Resource Set Roles and Permissions”](#about-resource-set-roles-and-permissions)

While a Resource Set is a collection of individual resources grouped together, within that same Resource Set, you will also need to assign users a specific role, and permissions for that role. When configuring Resource Sets, consider the following:

* Roles should be assigned to users based on their responsibilities for managing the Resource Set. When thinking of roles and role assignments, consider the role assignment from a resource-first perspective.

* Permissions should be granted for each Role to ensure the user can perform their required tasks. Permissions in a role work with the Resource Set association to enable access to specific Resource Set entities as configured.

## Additional resources

[Section titled “Additional resources”](#additional-resources)

The following pages provide more information about working with Resource Sets:

* [Creating Resource Sets](create-resource-set.md) - Learn how to create Resource Sets
* [Adding Resources to Resource Sets](adding-resources-to-resource-set.md) - Add resources to your Resource Sets
* [Assign Roles](assign-roles.md) - Assign roles to your Resource Sets
* [Deploying Resource Sets](deploy-resource-set.md) - Deploy your Resource Sets
* [Deleting Resource Sets](delete-resource-set.md) - Delete a custom Resource Set and everything it contains
