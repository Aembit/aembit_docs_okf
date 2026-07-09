---
type: explanation
title: "About Auditing and reporting"
description: "Understanding Aembit's auditing and reporting capabilities for workload access monitoring and compliance"
resource: https://docs.aembit.io/get-started/concepts/audit-report/
tags: [concept]
timestamp: 2026-04-13T13:05:24-07:00
type_inferred: true
---

# About Auditing and reporting

**Auditing and reporting** in Aembit provides comprehensive visibility into workload access patterns, administrative changes, and policy evaluation decisions through centralized, identity-centric logging. Unlike traditional logging methods that focus on network artifacts or secrets management events, Aembit’s approach centers on verified workload identities to create clear audit trails.

The platform captures three distinct types of events: administrative changes through Audit Logs, high-level workload interactions through Workload Events, and detailed policy evaluation steps through Access Authorization Events. This tiered logging structure enables organizations to monitor both operational workload behavior and administrative governance activities across their distributed environments.

Aembit’s auditing capabilities serve multiple critical functions: operational monitoring and troubleshooting, security incident response and forensics, and compliance with frameworks like NIST SP 800-171. The identity-first logging philosophy simplifies attribution and correlation in dynamic environments with ephemeral workloads, providing a single source of intelligence for workload access reviews.

![](https://docs.aembit.io/aembit-icons/gears-light.svg)

[Start exploring audit and reporting ](../../user-guide/audit-report/overview.md)See Audit & Report in the User Guide

→

## How auditing and reporting works

[Section titled “How auditing and reporting works”](#how-auditing-and-reporting-works)

The following steps outline how Aembit captures and processes audit information throughout the access control lifecycle:

1. **Access Attempt** - As workloads attempt access and administrators make changes, Aembit generates structured log events capturing the verified identity of participants, actions performed, and contextual information.

2. **Identity Attribution** - Aembit anchors each workload event to a cryptographically verified workload or rather than relying solely on network addresses or temporary tokens, providing clear attribution in dynamic environments.

3. **Tiered Categorization** - Aembit categorizes events into three distinct types: Audit Logs for administrative changes, Workload Events for high-level interactions, and Access Authorization Events for detailed policy evaluation steps.

4. **Contextual Enrichment** - Events include rich contextual metadata such as security posture checks, geographical information, time-based conditions, and environmental attributes to support comprehensive analysis.

5. **Authorization Events** - Access Authorization Events provide granular visibility into each step of policy evaluation, including Trust Provider attestation, Access Condition verification, and Credential Provider results.

6. **Internal Analysis** - Aembit makes events available through the Admin Dashboard for at-a-glance monitoring and dedicated reporting interfaces for detailed investigation with filtering and search capabilities.

7. **Centralized Collection** - Aembit collects all events centrally within Aembit Cloud, providing a unified view across heterogeneous environments and deployment models.

8. **External Export** - Log Streams enable continuous export of events to external systems like AWS S3 and Google Cloud Storage for integration with Security Information and Event Management (SIEM) and Security Orchestration, Automation, and Response (SOAR) platforms and long-term retention.

The following diagram illustrates this process:

![Diagram showing the auditing and reporting flow from client workloads and admin interface through Aembit Cloud event processing to external systems](https://docs.aembit.io/d2/docs/get-started/concepts/audit-report-0.svg)

## Supported event types and analysis tools

[Section titled “Supported event types and analysis tools”](#supported-event-types-and-analysis-tools)

Aembit provides multiple event types and analysis interfaces tailored for different monitoring and investigation needs:

**Event Types**

* [Audit Logs](../../user-guide/audit-report/audit-logs.md) - Track administrative changes including policy modifications, user management, and configuration updates with administrator identity, timestamps, and affected resources
* Workload Events - Monitor high-level workload interactions with severity levels (Info, Warning, Error) while excluding sensitive payload data for privacy
* [Access Authorization Events](../../user-guide/audit-report/access-authorization-events.md) - Provide granular visibility into each step of policy evaluation including Trust Provider attestation, Access Condition verification, and Credential Provider results

**Internal Analysis Tools**

* [Admin Dashboard](../../user-guide/administration/admin-dashboard/overview.md) - At-a-glance visibility through summary panels, recent activity widgets, and trend analysis for quick operational awareness
* [Dedicated reporting interfaces](../../user-guide/audit-report/overview.md) - Detailed event exploration with filtering by time range, severity, workload identity, and Resource Set for focused investigation

**External Integration**

* [Log Streams to AWS S3](../../user-guide/administration/log-streams/aws-s3.md) - Continuous export of events to Amazon S3 buckets for SIEM integration and long-term storage
* [Log Streams to Google Cloud Storage](../../user-guide/administration/log-streams/gcs-bucket.md) - Export events to Google Cloud Storage (GCS) buckets for analysis in Google Cloud-based security tools
* [SIEM integrations](../../user-guide/administration/log-streams/overview.md) - Configuration guidance for Splunk, Microsoft Sentinel, and other security platforms

## Benefits of using auditing and reporting

[Section titled “Benefits of using auditing and reporting”](#benefits-of-using-auditing-and-reporting)

* **Identity-Centric Attribution** - Links all events to verified workload or administrator identities rather than network artifacts, providing clear accountability in dynamic environments with ephemeral workloads.
* **Comprehensive Visibility** - Captures both operational workload interactions and administrative governance activities through a unified logging framework across heterogeneous environments.
* **Compliance Support** - Provides detailed audit trails meeting requirements for frameworks like NIST SP 800-171 with structured records supporting accountability and access enforcement verification.
* **Troubleshooting Efficiency** - Enables rapid identification of policy evaluation failures through granular Access Authorization Events that pinpoint exact failure points in complex policy logic.
* **Security Investigation** - Delivers rich contextual information including security posture checks, geographical data, and environmental attributes essential for incident response and forensic analysis.
