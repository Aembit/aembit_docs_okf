---
type: how-to
title: "Using multiple Client Workload identifiers"
description: "How to use multiple Client Workload identifiers to increase uniqueness across Client Workloads"
resource: https://docs.aembit.io/user-guide/access-policies/client-workloads/identification/client-workload-multiple-ids/
interface: web-ui
tags: [identification, client-workload, access-policy]
timestamp: 2025-06-06T13:01:11-04:00
type_inferred: true
---

# Using multiple Client Workload identifiers

Aembit supports configuring multiple identifiers for a single Client Workload.

Identifying Client Workloads using multiple identifiers allows you to create highly specific and granular identification criteria for workloads that reside in complex environments that span multiple clouds, networks, and Kubernetes clusters.

By combining different identifiers, such as [Hostname](hostname.md) and [Process Name](process-name.md) on a Virtual Machine, you can uniquely pinpoint a specific application running on a particular machine. This enhanced uniqueness helps Aembit more accurately determine which workloads it must evaluate across complex environments where certain identifiers may be the same.

For example, more generic identifiers like [AWS Account ID](aws-account-id.md) or [Azure Subscription ID](azure-subscription-id.md) may be the same for some of your resources. Using just one of these identifiers would likely cause Aembit to misidentify workloads your environment.

Using multiple identifiers helps reduce the possibility of misidentification or overly permissive matching that might occur if you use only a single, non-unique identifier. This, in turn, strengthens your security posture.

Aembit highly recommends that you leverage multiple identifiers where a single method might be ambiguous, to make sure Aembit can uniquely identify workloads and prevent misidentification.

## How multiple identifiers work

[Section titled “How multiple identifiers work”](#how-multiple-identifiers-work)

When you configure multiple identifiers for a *single* Client Workload, Aembit uses the conditional operators `AND` and `OR`. You can use one or the other or both at the same time.

### The `OR` condition

[Section titled “The OR condition”](#the-or-condition)

When Aembit uses the `OR` condition, it requires only one of the identifiers, providing you extra flexibility. You can have multiple `OR` condition groups for a single Client Workload. This means that Aembit must match *only one* of the identification methods you’ve configured on your Client Workload to the evidence it collected from your runtime environment.

For example, combining a **AWS Account ID** identifier with a **Process Name** identifier for a Virtual Machine workload. In this scenario, Aembit would require *either* the AWS Account ID *or* the Process Name of the requesting Client Workload to match the values you’ve configured in the Client Workload definition for Aembit to consider that definition a match.

### The `AND` condition

[Section titled “The AND condition”](#the-and-condition)

When Aembit uses the `AND` condition, it requires both identifiers, providing you extra security. You can have multiple `AND` condition groups for a single Client Workload. This means that Aembit must match *all* the identification methods you’ve configured on your Client Workload to the evidence it collected from your runtime environment.

For example, combining a **Hostname** identifier with a **Process Name** identifier for a Virtual Machine workload. In this scenario, Aembit would require *both* the Hostname *and* the Process Name of the requesting Client Workload to match the values you’ve configured in the Client Workload definition for Aembit to consider that definition a match.

### Both conditions

[Section titled “Both conditions”](#both-conditions)

When Aembit uses both the `OR` and the `AND` conditions together, you can create sophisticated identification logic that provides both *security and flexibility* for your Client Workload definitions. You can combine multiple `OR` and `AND` condition groups within a single Client Workload configuration. This allows you to define complex matching criteria where some identifiers must all be present (`AND` groups) while providing alternative identification paths (`OR` groups).

You might use this when the same application runs in multiple environments, but you want both scenarios to access the same resources through a single Client Workload definition.

For example, you have two separate AWS Accounts that deploy the same application in one AWS Region on multiple hosts that need to connect to the same resource. You’d configure a Client Workload with the following logic:

(**AWS Account ID-1** `OR` **AWS Account ID-2**) `AND` (**AWS Region** `AND` **Hostname**)

Which would look like the following screenshot when you configure it in your Aembit Tenant:

![Client Workload multiple identifiers](https://docs.aembit.io/_astro/client-workload-multiple-ids.hpbLkrbr_ZhyN7e.webp)

In this scenario, Aembit would consider the Client Workload definition a match when both:

* Either **AWS Account ID-1** `OR` **AWS Account ID-2** match the configured values

* Both the **AWS Region** `AND` **Hostname** match the configured values

This approach enables you to accommodate different deployment scenarios while maintaining strong identity verification.

## Add additional identifiers to a Client Workload

[Section titled “Add additional identifiers to a Client Workload”](#add-additional-identifiers-to-a-client-workload)

To add additional identifiers to a Client Workload, follow these steps:

1. Create a new Client Workload or edit an existing one in your Aembit Tenant.
2. In the **Client Identification** section, click **+ Additional Client Identifier**.
3. Select the identifier type you want to add from the dropdown menu.
4. Enter the value for the identifier.
5. If you want to add another identifier, repeat steps 2-4.
6. Click **Save** to apply the changes to the Client Workload. Aembit displays the updated Client Workload with the new identifiers on the **Client Workloads** page.
