---
type: explanation
title: "Metadata collection"
description: "The minimal metadata Aembit collects to operate the platform, and the analytics this documentation site collects."
resource: https://docs.aembit.io/get-started/security-posture/metadata-collection/
tags: ["security-posture"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Metadata collection

Aembit values transparency about the information it handles. Aembit strives to collect and store only the minimal information required to deliver the service and to satisfy customer requests. This page summarizes two things: the metadata Aembit collects to operate the platform, and the analytics this documentation website collects.

> **Authoritative reference**
>
> This page is a plain-language summary. For the complete and legally authoritative description of how Aembit collects, uses, and protects information, see the [Aembit Privacy Policy](https://aembit.io/privacy-policy/).

## Metadata Aembit collects

To broker and authorize workload access, Aembit collects only the minimal metadata required.

### Administrative users

For the people who administer Aembit in the [Aembit Cloud](../concepts/overview.md):

* **Email address**
* **Name**
* **IP address**

This information identifies administrators, secures access to the Aembit Cloud, and produces the audit trail of administrative actions. Administrator IP addresses appear in the audit record as the **Client IP** of each action. See [Audit logs](../../user-guide/audit-report/audit-logs.md).

### Workloads

For the client and server workloads that Aembit brokers access between:

* **IP address**
* **System-specific identifiers**, such as hostname and pod name

Aembit uses these attributes to establish and verify workload identity and to record access authorization events. The attributes that apply depend on how you identify a workload, such as by [hostname](../../user-guide/access-policies/client-workloads/identification/hostname.md) or [Kubernetes pod details](../../user-guide/access-policies/client-workloads/identification/kubernetes-service-account-name.md). Aembit records workload network details, including client and server IP addresses, in [access authorization events](../../user-guide/audit-report/access-authorization-events.md).

> **Where this fits**
>
> Aembit collects this metadata to run the service you configure, not for advertising or profiling. For how Aembit protects it, see [Security compliance](security-compliance.md) and the [Aembit Trust Center](https://trust.aembit.io/).

## Documentation site analytics

This documentation website also collects analytics data, distinct from the platform, to understand how visitors use the documentation so Aembit can improve it.

**These analytics load only after you accept them.** When you first visit, a consent banner asks for your choice. Nothing loads until you accept, and you can change your decision at any time using the **Cookie preferences** link in the page footer.

Aembit and its third-party partners use analytics services such as Google Analytics to collect and process certain analytics data using cookies and similar tracking technologies. This includes:

* **Pages you view** and how you navigate the documentation
* **Approximate location**, derived from your IP address (such as country, region, and city)
* **Device and browser information**, such as browser type, operating system, and screen size
* **On-page interactions**, such as searches, link clicks, and how far you scroll

This documentation-site analytics data **isn’t tied to your Aembit account and doesn’t identify you personally**. It reflects anonymous usage of the documentation, not your activity within the Aembit platform.

> **Managing your choice**
>
> You can decline analytics at any time by opening **Cookie preferences** in the footer and choosing to turn analytics off. For details on how Aembit handles this data, see the [Aembit Privacy Policy](https://aembit.io/privacy-policy/).
