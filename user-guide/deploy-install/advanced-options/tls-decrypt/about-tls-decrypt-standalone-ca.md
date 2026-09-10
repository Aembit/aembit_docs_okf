---
type: explanation
title: "About Standalone CA for TLS Decrypt"
description: "How to configure TLS Decrypt with a Standalone CA"
resource: https://docs.aembit.io/user-guide/deploy-install/advanced-options/tls-decrypt/about-tls-decrypt-standalone-ca/
tags: ["tls-decrypt", "advanced-option", "deploy-install"]
timestamp: 2026-09-08T23:32:41-07:00
---

# About Standalone CA for TLS Decrypt

Standalone Certificate Authorities (CAs) function as dedicated, isolated entities that grant you more granular control over managing [TLS Decrypt](overview.md). With Standalone CAs, you can create, assign, and manage unique CAs what are independent from Aembit’s default CAs to precisely manage TLS traffic.

You can assign Standalone CAs to specific resources (such as Client Workloads or [Resource Sets](../../../administration/resource-sets/overview.md)) rather than tying those resources to your Aembit configuration at the Tenant-level.

To set up a Standalone CA, see [How to configure Standalone CA for TLS Decrypt](configure-tls-decrypt-standalone-ca.md).

## Important terminology

**Trust model** - A set of rules and configurations that define which CAs are trusted within a given context. In the context of Aembit’s TLS Decrypt feature, a trust model determines whether Aembit uses a Tenant-level CA, a Standalone CA, or both to validate TLS certificates.

**Trust boundary** - The defined scope within which a CA is trusted. By assigning a Standalone CA to a Resource Set, you create a distinct trust boundary that isolates that Resource Set’s workloads from other environments.

## How Standalone CAs work

Standalone CAs provide a decentralized approach to certificate management by allowing individual resources to define their own trusted CAs rather than relying on a single Tenant-wide CA.

After you create and assign a Standalone CA to a Resource Set, it establishes a distinct trust boundary, making sure that workloads in separate Resource Sets operate independently. This isolation makes it so different Resource Sets don’t rely on the same root certificate. It also reduces the risk of unintended certificate exposure by limiting each CA’s visibility and application to its defined scope.

Additionally, assigning a Standalone CA directly to a Client Workload overrides any Resource Set or Tenant-level CA, providing a way to enforce unique trust requirements for workloads that require separate security controls.

If you don’t assign a Standalone CA to a Client Workload or its associated Resource Set, Aembit automatically falls back to the Tenant-level CA. This fallback makes sure workloads can still establish trusted TLS connections even if no Standalone CA is explicitly configured, maintaining continuity in certificate management.

## Standalone CA assignment

You have two options when assigning a Standalone CA:

* **Assign to a Resource Set** - Assigning a Standalone CA to a Resource Set isolates its trust model and establishes a shared trust boundary for all workloads within that set. This makes sure that only workloads within that Resource Set rely on the selected CA.

* **Assign to a Client Workload** - By explicitly assigning a Standalone CA to a Client Workload, you can override the Tenant-level CA or Standalone CA set at the Resource Set-level. This assignment takes precedence over the Resource Set’s CA, giving you have fine-grained control over TLS decryption behavior on individual Client Workloads.

This layered structure allows you to establish both broad certificate policies via Resource Sets and targeted overrides for specific Client Workloads.

## How Aembit chooses which CA to use

Aembit resolves certificate authorities during the TLS Decrypt process from most to least restrictive:

1. **Client Workload Level** - Aembit first checks for a Standalone CA assigned directly to the requesting Client Workload.

2. **Resource Set Level** - If Aembit doesn’t find a workload-specific CA, it checks for a CA assigned to the workload’s Resource Set.

3. **Tenant Level** - If you’ve not assigned a Standalone CA at either level, Aembit defaults to using the Tenant-level CA.

This hierarchical approach allows targeted overrides for specific workloads while preserving the broader certificate structure across your infrastructure.

Expand for a complex example

Imagine an organization that operates multiple environments for development, staging, and production, each managed within its own Resource Set.

In this setup, the production Resource Set has a Standalone CA configured to enforce stricter security controls. A critical backend API within this Resource Set also has a Standalone CA assigned directly to it, designed to meet its unique certificate requirements. Meanwhile, the development and staging Resource Sets have no Standalone CAs assigned.

If a workload in the production Resource Set attempts to establish a TLS connection:

* Aembit first checks for a Standalone CA assigned directly to that workload. Since the backend API has its own CA, that certificate is used.

* If the workload didn’t have a workload-specific CA, Aembit would default to the production Resource Set’s Standalone CA.

* If no Standalone CA were assigned to either the workload or the Resource Set, Aembit would instead use the Tenant-level CA.

Meanwhile, workloads in the development and staging Resource Sets would skip the first two steps, defaulting directly to the Tenant-level CA since no Standalone CAs are defined.

This hierarchy allows the organization to enforce stricter security controls for critical services while maintaining simpler certificate management in less sensitive environments.

## Best practices for Standalone CAs

* **Use Standalone CAs for Critical Resources** - For sensitive services requiring stricter control, Standalone CAs improve isolation and minimize certificate sprawl.

* **Define Clear Certificate Lifetimes** - Setting appropriate expiration periods reduces exposure to outdated certificates.

* **Audit and Monitor CA Usage** - Periodically review CA associations to maintain secure and predictable TLS decryption behavior.

* **Keep organization consistent** - Consistency matters for predictable TLS decryption behavior, so align Standalone CA assignments with your infrastructure’s organizational structure.

* **Simplify where you can** - While scoping CAs narrowly can reduce exposure, consolidating similar workloads under a shared Resource Set can simplify certificate management.

## Scoping Standalone CAs too tightly

While tightly scoped Standalone CAs improve security and isolation, they can increase operational complexity. Managing multiple narrowly scoped CAs requires careful tracking of certificate rotations and renewals. Frequent resource movement across environments may lead to mismatched CA associations, disrupting communication. Additionally, troubleshooting becomes more complex when multiple isolated trust boundaries exist.

Balance security with operational efficiency when defining CA scopes.

## Standalone CA behavior

When managing Standalone CAs, it’s crucial to understand how Resource Sets influence their behavior. Resource Sets define the scope within which a Standalone CA is trusted, which directly impacts both certificate visibility and Client Workload associations.

### In Resource Sets

* **Consider trust boundary establishment** - Assigning a Standalone CA to a Resource Set creates a distinct trust boundary, with all Client Workloads in that Resource Set inheriting the assigned CA unless overridden.

* **Plan for certificate isolation** - Maintain unique Standalone CAs for different Resource Sets to prevent certificate trust from extending across unrelated workloads.

* **Beware of resource portability risks** - Moving workloads between Resource Sets may break certificate trust unless the new Resource Set shares the same Standalone CA or you reconfigure it.

### In Client Workloads

* **Use targeted overrides strategically** - Assign a Standalone CA directly to a Client Workload to override the Resource Set’s CA only when workloads have distinct security requirements.

* **Watch for inconsistent trust models** - Carefully coordinate workload-level CA assignments to avoid creating fragmented trust models and certificate mismatches.

* **Remember the tenant-level fallback** - If you don’t assign a Standalone CA to either the Resource Set or Client Workload, Aembit defaults to using the Tenant-level CA.

By thoughtfully aligning Standalone CA assignments with your Resource Sets and workload structure, you can achieve stronger security without adding unnecessary complexity.

## Additional resources

* [About TLS Decrypt](overview.md)

* [Configure a Standalone CA](configure-tls-decrypt-standalone-ca.md)
