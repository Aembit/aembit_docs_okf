---
type: explanation
title: "About Access Conditions"
description: "Understanding Access Conditions and their role in context-aware authorization"
resource: https://docs.aembit.io/get-started/concepts/access-conditions/
tags: ["concept"]
timestamp: 2026-09-08T23:32:41-07:00
---

# About Access Conditions

Access Conditions add dynamic, context-aware constraints to the authorization process in Aembit Access Policies.

They evaluate the circumstances surrounding each access request—such as time, location, or security posture—to determine whether to grant access.

While Trust Providers verify “who” is making the request, Access Conditions evaluate “when,” “where,” and “under what security conditions” to allow the request. This provides Multi-Factor Authentication (MFA)-like security for workload interactions by requiring both verified identity and verified context.

Aembit evaluates Access Conditions after confirming workload identity but before issuing any credentials. This placement ensures that sensitive access tokens are only generated when both the workload’s identity and its operational context meet policy requirements.

![](https://docs.aembit.io/aembit-icons/access-condition.svg)

[Start configuring Access Conditions](../../user-guide/access-policies/access-conditions/overview.md)See Access Conditions in the User Guide

→

## How Access Conditions work

The following steps outline how Aembit evaluates Access Conditions during the authorization process:

1. **Request Initiation** - A Client Workload attempts to access a Server Workload.

2. **Identity Verification** - Aembit Edge sends identity evidence to Aembit Cloud, where [Trust Providers](trust-providers.md) verify the Client Workload’s identity through workload attestation.

3. **Context Gathering** - Access Conditions gather contextual information from multiple sources (time, location, security tools). Aembit caches context data it collects from thrid-party security tools in Aembit Cloud to avoid latency and unnecessary API calls on every access request.

4. **Context Evaluation** - Access Conditions evaluate the gathered context against configured rules to determine if the request meets policy requirements.

5. **Authorization Decision** - If all Access Conditions pass, Aembit proceeds to credential issuance. If any condition fails, Aembit immediately denies access.

6. **Credential Issuance** - Only after successful context verification does Aembit invoke the Credential Provider to issue access credentials.

The following diagram illustrates this process:

![Diagram showing how Aembit evaluates Access Conditions during the authorization process](https://docs.aembit.io/d2/docs/get-started/concepts/access-conditions-0.svg)

## Supported condition types

Aembit supports multiple types of Access Conditions that allow you to control access based on different contextual factors:

### Time-based conditions

[Time conditions](../../user-guide/access-policies/access-conditions/aembit-time-condition.md) restrict access to specific schedules, such as business hours or maintenance windows. These conditions compare the current time (in a specified timezone) against configured allowed time ranges.

**Common use cases:**

* Limiting development tool access to production systems during business hours only
* Restricting automated batch jobs to specific maintenance windows
* Enforcing “follow the sun” access patterns for global teams

### Geographic GeoIP conditions

[GeoIP conditions](../../user-guide/access-policies/access-conditions/aembit-geoip.md) restrict access based on the geographic location of the request’s source IP address. Aembit determines location using integrated GeoIP databases and compares it against allowed countries and subdivisions.

**Common use cases:**

* Ensuring data sovereignty compliance (EU data accessed only from EU locations)
* Blocking access from high-risk geographic regions
* Enforcing regional access boundaries for compliance requirements

### Security posture conditions

Security posture conditions evaluate the rapid security health of the Client Workload’s environment by integrating with third-party security tools. These conditions make API calls to security platforms and evaluate their responses against configured requirements.

**Supported integrations:**

* **[Wiz](../../user-guide/access-policies/access-conditions/wiz.md)** - Verifies cloud security posture, including cluster connectivity and monitoring status
* **[CrowdStrike](../../user-guide/access-policies/access-conditions/crowdstrike.md)** - Confirms endpoint protection status, agent health, and host attributes

**Common use cases:**

* Blocking access from hosts with outdated security agents
* Preventing compromised or non-compliant systems from accessing sensitive resources
* Enforcing Zero Trust policies that require continuous security verification

## Benefits of using Access Conditions

* **Enhanced Security** - Provides MFA-like protection for workloads by requiring both identity and context verification before granting access.

* **Zero Trust Implementation** - Enables continuous verification of context on every access request, moving beyond static identity-based authorization.

* **Compliance Support** - Helps meet regulatory requirements for data sovereignty, access timing, and security posture verification.

* **Risk Reduction** - Prevents access from compromised or non-compliant environments, reducing the risk of lateral movement in security incidents.

* **Operational Flexibility** - Allows fine-grained control over when, where, and under what conditions workloads can access resources without modifying application code.

* **Audit Trail** - Provides detailed logging of context evaluation results for security monitoring and compliance reporting.
