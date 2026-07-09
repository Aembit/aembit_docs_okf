---
type: explanation
title: "Understanding component copying between Resource Sets"
description: "Learn what component copying is, what gets copied, and use cases for replicating configurations"
resource: https://docs.aembit.io/user-guide/administration/resource-sets/about-component-copying/
tags: [resource-set, administration]
timestamp: 2026-01-30T00:27:24-08:00
---

# Understanding component copying between Resource Sets

Component copying enables you to replicate Access Policy**Access Policy**: Access Policies define, enforce, and audit access between Client and Server Workloads by cryptographically verifying workload identity and contextual factors rather than relying on static secrets.[Learn more](../../../get-started/concepts/access-policies.md) components from one Resource Set**Resource Set**: Resource Sets are organizational containers that group Access Policy components together, enabling you to manage configurations across different environments, regions, or use cases.[Learn more](overview.md) to another. This feature addresses a critical workflow gap for enterprise tenants managing complex deployment topologies across multiple environments.

## How component copying works

[Section titled “How component copying works”](#how-component-copying-works)

When you copy a component, the system creates an independent duplicate in your target Resource Set. The original component remains unchanged in the source Resource Set, and the new copy receives its own unique identifier. This approach lets you replicate proven configurations without risking your live deployments.

### Copyable components

[Section titled “Copyable components”](#copyable-components)

You can copy the following Access Policy components between Resource Sets:

| Component                                                                                                                                                                                                                                                                                      | What gets copied                                                     |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------- |
| Client Workload**Client Workload**: Client Workloads represent software applications, scripts, or automated processes that initiate access requests to Server Workloads, operating autonomously without direct user interaction.[Learn more](../../../get-started/concepts/client-workloads.md)           | Configuration and Standalone Certificate Authority (CA) associations |
| Server Workload**Server Workload**: Server Workloads represent target services, APIs, databases, or applications that receive and respond to access requests from Client Workloads.[Learn more](../../../get-started/concepts/server-workloads.md)                                                        | All application protocol configurations                              |
| Trust Provider**Trust Provider**: Trust Providers validate Client Workload identities through workload attestation, verifying identity claims from the workload's runtime environment rather than relying on pre-shared secrets.[Learn more](../../../get-started/concepts/trust-providers.md)            | All types and match rule configurations                              |
| Credential Provider**Credential Provider**: Credential Providers obtain the specific access credentials—such as API keys, OAuth tokens, or temporary cloud credentials—that Client Workloads need to authenticate to Server Workloads.[Learn more](../../../get-started/concepts/credential-providers.md) | All types and configurations (3LO providers require reauthorization) |
| Access Condition**Access Condition**: Access Conditions add dynamic, context-aware constraints to authorization by evaluating circumstances like time, location, or security posture to determine whether to grant access.[Learn more](../../../get-started/concepts/access-conditions.md)                | Configuration and integration associations                           |
| Access Policy                                                                                                                                                                                                                                                                                  | The policy and all related components in this table                  |

When you copy an Access Policy, the system copies all related components together, creating a complete, self-contained policy in the target Resource Set.

## Component copying behaviors

[Section titled “Component copying behaviors”](#component-copying-behaviors)

Understanding what copying components does and doesn’t do helps set correct expectations.

### What copying components does

[Section titled “What copying components does”](#what-copying-components-does)

* **Preservation** - Original components in the source Resource Set remain unchanged and fully functional.
* **New identity** - Each copied component receives a new unique identifier in the target Resource Set.
* **Access control** - You can only copy to Resource Sets you have permission to access.
* **Source exclusion** - The system excludes the source Resource Set from the target selection list to encourage reusing existing configurations within the same Resource Set.

### What copying doesn’t do

[Section titled “What copying doesn’t do”](#what-copying-doesnt-do)

* **Doesn’t move components** - Copying creates a duplicate; the original stays in place.
* **Doesn’t manage Edge deployment** - After copying, you manage where and how to deploy new components to Aembit Edge**Aembit Edge**: Aembit Edge represents components deployed within your operational environments that enforce Access Policies by intercepting traffic, verifying identities, and injecting credentials just-in-time.[Learn more](../../../get-started/concepts/aembit-edge.md).
* **Doesn’t auto-bind to Edge components** - You must configure Edge bindings in the target Resource Set.
* **Doesn’t allow same-Resource-Set copies** - You can’t copy a component to the same Resource Set it already exists in.

## Use cases

[Section titled “Use cases”](#use-cases)

Component copying supports the following enterprise workflows:

### Promoting configurations between environments

[Section titled “Promoting configurations between environments”](#promoting-configurations-between-environments)

Copy configurations from a staging Resource Set to a production Resource Set. This lets you test and validate configurations in a safe environment before deploying them to production.

### Regional deployments

[Section titled “Regional deployments”](#regional-deployments)

Deploy identical Access Policy configurations to new AWS regions or cloud environments. Each region can have its own Resource Set with copies of your proven configurations.

### Safe experimentation

[Section titled “Safe experimentation”](#safe-experimentation)

Copy a configuration into a sandbox Resource Set to test significant changes without affecting live workloads. If the experiment fails, the original configuration remains untouched.

## Special considerations

[Section titled “Special considerations”](#special-considerations)

The following special considerations apply when copying components.

### Reauthorize 3LO Credential Providers

[Section titled “Reauthorize 3LO Credential Providers”](#reauthorize-3lo-credential-providers)

Credential Providers that use OAuth 2.0 Authorization Code flow (3LO**3LO**: 3-legged OAuth (3LO) is the OAuth 2.0 Authorization Code flow where a user explicitly authorizes an application to access their data on a third-party service, requiring user interaction to complete the authorization.[Learn more](../../access-policies/credential-providers/oauth-authorization-code.md)) have associations with third-party systems. After copying a 3LO Credential Provider, you must reauthorize it with the third-party system before you can use it.

### Client Workload uniqueness

[Section titled “Client Workload uniqueness”](#client-workload-uniqueness)

Client Workloads must have unique client identification values within a Resource Set. If the target Resource Set already has a Client Workload with the same client identification type and value, that Client Workload won’t copy.

### Your responsibilities after copying

[Section titled “Your responsibilities after copying”](#your-responsibilities-after-copying)

After copying components to a target Resource Set, you are responsible for:

* Modifying target-specific attributes as needed
* Authorizing any 3LO Credential Providers
* Binding components to Aembit Edge deployments
* Managing the new deployment lifecycle

## Related resources

[Section titled “Related resources”](#related-resources)

* [Copy components to another Resource Set](copy-components.md) - Step-by-step procedures for copying components
* [Resource Sets overview](overview.md) - Learn about Resource Sets and how they work
