---
type: explanation
title: "Global Policy Compliance Overview"
description: "What is Aembit Global Policy Compliance and how it works"
resource: https://docs.aembit.io/user-guide/administration/global-policy/
tags: [global-policy, administration]
timestamp: 2025-06-03T16:56:07-07:00
type_inferred: true
---

# Global Policy Compliance Overview

Aembit’s Global Policy Compliance is a security enforcement feature that allows administrators to establish organization-wide security standards for Access Policies and Agent Controllers. Global Policy Compliance ensures consistent security practices across your Aembit environment and prevents the creation of policies that might inadvertently expose resources.

## What Global Policy Compliance does

[Section titled “What Global Policy Compliance does”](#what-global-policy-compliance-does)

Global Policy Compliance provides centralized control over the following Aembit administration components:

### Access Policies

[Section titled “Access Policies”](#access-policies)

* **Trust Provider Requirements** - Ensures all Access Policies include proper identity verification
* **Access Condition Requirements** - Enforces contextual access rules across all policies

### Agent Controllers

[Section titled “Agent Controllers”](#agent-controllers)

* **Trust Provider Requirements** - Ensures proper identity verification for all Agent Controllers
* **TLS Hostname Requirements** - Enforces secure communication standards

## How Global Policy Compliance works

[Section titled “How Global Policy Compliance works”](#how-global-policy-compliance-works)

You can [configure Global Policy Compliance](manage-global-policy.md) to either require, recommend, or not enforce that Aembit components such as Access Policies have certain configurations. For example, you can set Global Policy Compliance to enforce that all Access Policies have a Trust Provider configured.

![Aembit Administration - Global Policy Compliance screen](https://docs.aembit.io/_astro/global-policy-settings.DrFjcm5S_Z259oOl.webp)

Global Policy Compliance operates on a three-tier enforcement model:

1. **Required** - Strictest setting - prevents creation or modification of non-compliant policies
2. **Recommended** (Default) - Flags non-compliant policies but allows their creation after confirmation
3. **Optional** - No enforcement - allows creation of policies without the specified security elements

Caution

Whenever you set a Global Policy Compliance setting to **Required**, Aembit prevents the creation or modification of Access Policies or Agent Controllers that don’t meet the specified requirements.

Enabling Global Policy Compliance settings to **Required** won’t deactivate existing Access Policies or Agent Controllers that don’t meet the requirements. However, you won’t be able to modify or save them until they become compliant.

## Global Policy Compliance status icons

[Section titled “Global Policy Compliance status icons”](#global-policy-compliance-status-icons)

Aembit visually identifies non-compliant Access Policies through color-coded status icons and labels:

* **Red** indicators for required but missing elements
* **Yellow** indicators for recommended but missing elements
* **Green** indicators for compliant Access Policies
* **Gray** indicators for disabled or not active Access Policies

## Review and audit compliance

[Section titled “Review and audit compliance”](#review-and-audit-compliance)

You can review and audit the compliance status of all Access Policies and Agent Controllers in your Aembit Tenant through the [Global Policy Compliance report dashboard](../../audit-report/global-policy.md).

## Benefits

[Section titled “Benefits”](#benefits)

* Ensures consistent security standards across your organization
* Prevents accidental creation of insecure Access Policies
* Provides visibility into policy compliance through visual indicators
* Supports role-based access control for compliance settings management

## Use cases

[Section titled “Use cases”](#use-cases)

Aembit’s Global Policy Compliance feature applies to many different use cases, such as the following:

* **Enterprise security compliance** - Security administrators in large enterprises can enforce that all Access Policies include proper identity verification through Trust Providers, ensuring consistent security practices across multiple teams and Resource Sets.

* **Regulated industries** - Organizations in healthcare, finance, and other regulated industries can use Global Policy Compliance to maintain audit-ready Access Policies that consistently implement required security controls.

* **DevOps security** - DevOps teams can implement secure-by-default practices by requiring Access Conditions on all policies, preventing deployment of resources with inadequate access controls.

* **Service providers** - Managed Service Providers (MSP) and SaaS providers can enforce strict TLS hostname requirements for Agent Controllers, ensuring secure communication standards across client environments.

## Additional resources

[Section titled “Additional resources”](#additional-resources)

-[Managing Policy Compliance](manage-global-policy.md)
