---
type: explanation
title: "Understanding component copying between Resource Sets"
description: "Learn what component copying is, what gets copied, and use cases for replicating configurations"
resource: https://docs.aembit.io/user-guide/administration/resource-sets/about-component-copying/
tags: ["resource-set", "administration"]
timestamp: 2026-09-09T08:20:13-07:00
---

# Understanding component copying between Resource Sets

Component copying enables you to replicate Access Policy components from one Resource Set to another. This feature addresses a critical workflow gap for enterprise tenants managing complex deployment topologies across multiple environments.

## How component copying works

When you copy a component, the system creates an independent duplicate in your target Resource Set. The original component remains unchanged in the source Resource Set, and the new copy receives its own unique identifier. This approach lets you replicate proven configurations without risking your live deployments.

### Copyable components

You can copy the following Access Policy components between Resource Sets:

| Component           | What gets copied                                                     |
| ------------------- | -------------------------------------------------------------------- |
| Client Workload     | Configuration and Standalone Certificate Authority (CA) associations |
| Server Workload     | All application protocol configurations                              |
| Trust Provider      | All types and match rule configurations                              |
| Credential Provider | All types and configurations (3LO providers require reauthorization) |
| Access Condition    | Configuration and integration associations                           |
| Content Security    | All provider types and their configurations                          |
| Access Policy       | The policy and all related components in this table                  |

When you copy an Access Policy, the system copies all related components together, creating a complete, self-contained policy in the target Resource Set. This includes any Content Security Provider on the policy.

## Component copying behaviors

Understanding what copying components does and doesn’t do helps set correct expectations.

### What copying components does

* **Preservation** - Original components in the source Resource Set remain unchanged and fully functional.
* **New identity** - Each copied component receives a new unique identifier in the target Resource Set.
* **Access control** - You can only copy to Resource Sets you have permission to access.
* **Source exclusion** - The system excludes the source Resource Set from the target selection list to encourage reusing existing configurations within the same Resource Set.

### What copying doesn’t do

* **Doesn’t move components** - Copying creates a duplicate; the original stays in place.
* **Doesn’t manage Edge deployment** - After copying, you manage where and how to deploy new components to Aembit Edge.
* **Doesn’t auto-bind to Edge components** - You must configure Edge bindings in the target Resource Set.
* **Doesn’t allow same-Resource-Set copies** - You can’t copy a component to the same Resource Set it already exists in.

## Use cases

Component copying supports the following enterprise workflows:

### Promoting configurations between environments

Copy configurations from a staging Resource Set to a production Resource Set. This lets you test and validate configurations in a safe environment before deploying them to production.

### Regional deployments

Deploy identical Access Policy configurations to new AWS regions or cloud environments. Each region can have its own Resource Set with copies of your proven configurations.

### Safe experimentation

Copy a configuration into a sandbox Resource Set to test significant changes without affecting live workloads. If the experiment fails, the original configuration remains untouched.

## Special considerations

The following special considerations apply when copying components.

### Reauthorize 3LO Credential Providers

Credential Providers that use OAuth 2.0 Authorization Code flow (3LO) have associations with third-party systems. After copying a 3LO Credential Provider, you must reauthorize it with the third-party system before you can use it.

### Client Workload uniqueness

Client Workloads must have unique client identification values within a Resource Set. If the target Resource Set already has a Client Workload with the same client identification type and value, that Client Workload won’t copy.

### Your responsibilities after copying

After copying components to a target Resource Set, you are responsible for:

* Modifying target-specific attributes as needed
* Authorizing any 3LO Credential Providers
* Binding components to Aembit Edge deployments
* Managing the new deployment lifecycle

## Related resources

* [Copy components to another Resource Set](copy-components.md) - Step-by-step procedures for copying components
* [Resource Sets overview](overview.md) - Learn about Resource Sets and how they work
