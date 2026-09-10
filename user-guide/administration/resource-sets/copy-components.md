---
type: how-to
title: "Copy components to another Resource Set"
description: "Step-by-step procedures for copying Access Policy components between Resource Sets"
resource: https://docs.aembit.io/user-guide/administration/resource-sets/copy-components/
interface: web-ui
tags: ["resource-set", "administration"]
timestamp: 2026-09-09T08:20:13-07:00
---

# Copy components to another Resource Set

This guide covers how to copy individual components and entire Access Policy between Resource Set.

## Prerequisites

To copy any components, you must have:

* Access to the Aembit Tenant UI
* Permission to access both the source and target Resource Sets
* At least one component to copy

For background on what component copying does and when to use it, see [Understanding component copying](about-component-copying.md).

## Copy an individual component

Copy any of these component types to another Resource Set:

* Client Workload
* Server Workload
* Trust Provider
* Credential Provider
* Access Condition
* Content Security

1. Navigate to the component you want to copy.
2. Open the component’s three-dot menu (**⋮**).
3. Select **Copy To**.
4. From the **Choose a Resource Set to Copy To** dropdown, select your target **Resource Set**.
5. (Optional) If the component uses a Standalone Certificate Authority (CA), toggle the **Copy Stand-Alone CA** slider to include it.
6. Click **Copy to selected**.

After copying completes, a toast notification appears:

* **Success**: “\[Component Name] has been copied successfully to the target resource set”
* **With CA**: “\[Component Name] and \[CA Name] has been copied successfully to the target resource set”

## Copy an Access Policy

When you copy an Access Policy, the system copies the policy and all its related components together. This includes any Client Workloads, Server Workloads, Trust Providers, Credential Providers, Access Conditions, and Content Security Providers associated with the policy.

1. Navigate to the Access Policy you want to copy.

2. Open the policy’s three-dot menu (**⋮**) in the upper right corner.

3. Select **Copy To**.

4. From the **Choose a Resource Set to Copy To** dropdown, select your target Resource Set.

5. (Optional) Toggle the **Copy Stand-Alone CA** slider to include any Standalone Certificate Authorities.

6. Click **Verify** to preview the components the system copies.

7. Review the verification list:

   * **Ready To Copy**: The system copies this component successfully
   * **Will Not Copy**: The system skips this component due to a uniqueness constraint

8. Click **Copy** to complete the operation.

After copying completes, a toast notification appears:

* **Success**: “\[Policy Name] and \[#] of \[#] entities have been copied successfully to the target resource set”
* **Partial success**: If the system skipped some components, the count reflects only the components that copied successfully

## After copying

After you copy components to a new Resource Set, you’re responsible for:

* **Modifying target-specific attributes** - Update any environment-specific values like URLs, endpoints, or identifiers
* **Authorizing 3LO Credential Providers** - If you copied Credential Providers that use OAuth 2.0 Authorization Code flow (3LO), reauthorize them with the third-party system
* **Binding to Aembit Edge** - Configure Edge bindings in the target Resource Set to deploy the copied components
* **Managing the deployment lifecycle** - The copied components are independent; changes to the original don’t affect the copy

## Client Workload uniqueness

Client Workloads must have unique client identification values within a Resource Set. If the target Resource Set already has a Client Workload with the same client identification type and value, that Client Workload won’t copy.

The Access Policy doesn’t copy either, because a policy can’t exist without its Client Workload. Aembit reports `Client Workload missing. Not Copied.` for the policy.

To resolve this, modify the client identification on either the source or target Client Workload before copying.

## Standalone certificate authorities

The **Copy Stand-Alone CA** toggle controls whether Standalone Certificate Authorities copy with your components. This applies to Client Workloads and Access Policies that use Standalone CAs.

| Toggle state | Behavior                                                                                        |
| ------------ | ----------------------------------------------------------------------------------------------- |
| On           | The Standalone CA associated with the component copies to the target Resource Set               |
| Off          | Only the component copies; the target Resource Set must already have a compatible CA configured |

Enable this toggle when you want the target Resource Set to have its own copy of the CA. Disable it when the target Resource Set already has the CA you need or shares CAs with the source.

## Related resources

* [Understanding component copying](about-component-copying.md) - Learn what copying does, use cases, and key behaviors
* [Resource Sets overview](overview.md) - Learn about Resource Sets and how they work
