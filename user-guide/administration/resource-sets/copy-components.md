---
type: how-to
title: "Copy components to another Resource Set"
description: "Step-by-step procedures for copying Access Policy components between Resource Sets"
resource: https://docs.aembit.io/user-guide/administration/resource-sets/copy-components/
interface: web-ui
tags: [resource-set, administration]
timestamp: 2026-01-30T00:27:24-08:00
---

# Copy components to another Resource Set

This guide covers how to copy individual components and entire Access Policy**Access Policy**: Access Policies define, enforce, and audit access between Client and Server Workloads by cryptographically verifying workload identity and contextual factors rather than relying on static secrets.[Learn more](../../../get-started/concepts/access-policies.md) between Resource Set**Resource Set**: Resource Sets are organizational containers that group Access Policy components together, enabling you to manage configurations across different environments, regions, or use cases.[Learn more](overview.md).

## Prerequisites

[Section titled “Prerequisites”](#prerequisites)

To copy any components, you must have:

* Access to the Aembit Tenant UI
* Permission to access both the source and target Resource Sets
* At least one component to copy

For background on what component copying does and when to use it, see [Understanding component copying](about-component-copying.md).

## Copy an individual component

[Section titled “Copy an individual component”](#copy-an-individual-component)

Copy a single component (Client Workload**Client Workload**: Client Workloads represent software applications, scripts, or automated processes that initiate access requests to Server Workloads, operating autonomously without direct user interaction.[Learn more](../../../get-started/concepts/client-workloads.md), Server Workload**Server Workload**: Server Workloads represent target services, APIs, databases, or applications that receive and respond to access requests from Client Workloads.[Learn more](../../../get-started/concepts/server-workloads.md), Trust Provider**Trust Provider**: Trust Providers validate Client Workload identities through workload attestation, verifying identity claims from the workload's runtime environment rather than relying on pre-shared secrets.[Learn more](../../../get-started/concepts/trust-providers.md), Credential Provider**Credential Provider**: Credential Providers obtain the specific access credentials—such as API keys, OAuth tokens, or temporary cloud credentials—that Client Workloads need to authenticate to Server Workloads.[Learn more](../../../get-started/concepts/credential-providers.md), or Access Condition**Access Condition**: Access Conditions add dynamic, context-aware constraints to authorization by evaluating circumstances like time, location, or security posture to determine whether to grant access.[Learn more](../../../get-started/concepts/access-conditions.md)) to another Resource Set.

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

[Section titled “Copy an Access Policy”](#copy-an-access-policy)

When you copy an Access Policy, the system copies the policy and all its related components together. This includes any Client Workloads, Server Workloads, Trust Providers, Credential Providers, and Access Conditions associated with the policy.

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

[Section titled “After copying”](#after-copying)

After you copy components to a new Resource Set, you’re responsible for:

* **Modifying target-specific attributes** - Update any environment-specific values like URLs, endpoints, or identifiers
* **Authorizing 3LO**3LO**: 3-legged OAuth (3LO) is the OAuth 2.0 Authorization Code flow where a user explicitly authorizes an application to access their data on a third-party service, requiring user interaction to complete the authorization.[Learn more](../../access-policies/credential-providers/oauth-authorization-code.md) Credential Providers** - If you copied Credential Providers that use OAuth 2.0 Authorization Code flow (3LO), reauthorize them with the third-party system
* **Binding to Aembit Edge**Aembit Edge**: Aembit Edge represents components deployed within your operational environments that enforce Access Policies by intercepting traffic, verifying identities, and injecting credentials just-in-time.[Learn more](../../../get-started/concepts/aembit-edge.md)** - Configure Edge bindings in the target Resource Set to deploy the copied components
* **Managing the deployment lifecycle** - The copied components are independent; changes to the original don’t affect the copy

## Client Workload uniqueness

[Section titled “Client Workload uniqueness”](#client-workload-uniqueness)

Client Workloads must have unique client identification values within a Resource Set. If the target Resource Set already has a Client Workload with the same client identification type and value, that Client Workload won’t copy.

To resolve this, modify the client identification on either the source or target Client Workload before copying.

## Standalone certificate authorities

[Section titled “Standalone certificate authorities”](#standalone-certificate-authorities)

The **Copy Stand-Alone CA** toggle controls whether Standalone Certificate Authorities copy with your components. This applies to Client Workloads and Access Policies that use Standalone CAs.

| Toggle state | Behavior                                                                                        |
| ------------ | ----------------------------------------------------------------------------------------------- |
| Enabled      | The Standalone CA associated with the component copies to the target Resource Set               |
| Disabled     | Only the component copies; the target Resource Set must already have a compatible CA configured |

Enable this toggle when you want the target Resource Set to have its own copy of the CA. Disable it when the target Resource Set already has the CA you need or shares CAs with the source.

## Related resources

[Section titled “Related resources”](#related-resources)

* [Understanding component copying](about-component-copying.md) - Learn what copying does, use cases, and key behaviors
* [Resource Sets overview](overview.md) - Learn about Resource Sets and how they work
