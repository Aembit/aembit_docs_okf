---
type: explanation
title: "Audit and report on Workload activity"
description: "This document provides a high-level conceptual overview of auditing and reporting workload activity"
resource: https://docs.aembit.io/user-guide/audit-report/
tags: [audit-report]
timestamp: 2026-05-19T19:12:05-07:00
type_inferred: true
---

# Audit and report on Workload activity

Your Aembit Tenant includes three different reporting tools that allow you to review detailed event information. These tools provide insights into your Aembit environment, enabling you to review historical event data and remediate any issues that may arise. This content is useful for reviewing the number of credential requests recorded over a specific period or diving deep into audit logs to troubleshoot errors.

The Aembit Tenant includes the following views in the Reporting Dashboard:

* [Access Authorization Events](#access-authorization-events)

* [Audit Logs](#audit-logs)

* [Workload Events](#workload-events)

* [MCP Authorization Tracing](#mcp-authorization-tracing)

* [Global Policy Compliance](#global-policy-compliance)

## Access Authorization Events

[Section titled “Access Authorization Events”](#access-authorization-events)

Aembit generates Access Authorization events when Edge Components request access to Aembit-managed Server Workloads. These events detail the evaluation of requests against Access Policies, including the request, evaluation steps, and the outcome (granted or denied). The three event types are: Access Request, Access Authorization, and Access Credential. These logs are essential for diagnosing access-related issues and detecting potential security threats.

![](https://docs.aembit.io/aembit-icons/lightbulb-light.svg)

[More on Access Authorization Events ](access-authorization-events.md)Learn how to review Access Authorization event information in the Reporting dashboard.

→

## Audit logs

[Section titled “Audit logs”](#audit-logs)

Audit logs capture detailed information about configuration and administrative activities within your Aembit Tenant. You can filter these logs by timespan, category, and severity to focus on specific events or time frames. The logs include timestamps, actors, categories, activities, targets, and results, that help you identify relevant events. This information, combined with client-specific details like IP address, browser, and operating system, provide you valuable context for troubleshooting and maintaining a comprehensive audit trail. This detailed logging also helps you identify the source of issues and understand the context of events within your Aembit environment.

![](https://docs.aembit.io/aembit-icons/lightbulb-light.svg)

[More on Audit Logs ](audit-logs.md)Learn how to review Audit Log information in the Reporting dashboard.

→

## Workload Events

[Section titled “Workload Events”](#workload-events)

Workload events enable a detailed view of network activities proxied by Aembit’s Agent Proxy. These events capture granular data related to the communication and interactions of workloads within your environment. By logging these activities, you gain insights into network traffic patterns, potential security anomalies, and the overall behavior of their workloads. This level of visibility is crucial for monitoring performance, troubleshooting network-related issues, and ensuring the secure operation of applications relying on Agent Proxy. The logged information typically includes details such as source and destination, timestamps, protocols, and any relevant metadata associated with the proxied network traffic.

![](https://docs.aembit.io/aembit-icons/lightbulb-light.svg)

[More on Workload Events ](workload-events.md)Learn how to review Workload event information in the Reporting dashboard.

→

## Global Policy Compliance

[Section titled “Global Policy Compliance”](#global-policy-compliance)

Use the Global Policy Compliance view to review the compliance status of your Aembit Tenant’s global policies. It enables you to identify any compliance issues and take necessary actions to ensure that your workloads align with your security and operational standards.

![](https://docs.aembit.io/aembit-icons/lightbulb-light.svg)

[More on Global Policy Compliance ](global-policy.md)Learn how to review Global Policy Compliance information in the Reporting dashboard.

→

## MCP Authorization Tracing

[Section titled “MCP Authorization Tracing”](#mcp-authorization-tracing)

MCP Authorization Tracing is a live view of inbound authorization requests at the MCP Identity Gateway. It surfaces the redirect URI, resource, matched Client Workload, and policy outcome for each request as the Gateway receives it, without waiting for the standard event-pipeline delay. Use it to reproduce a failing MCP flow and watch what the Gateway sees in real time.

![](https://docs.aembit.io/aembit-icons/lightbulb-light.svg)

[More on MCP Authorization Tracing ](mcp-authorization-tracing.md)Learn how to use the live MCP Authorization Tracing view in the Reporting dashboard.

→

For end-to-end troubleshooting of MCP and AI IAM access failures, see [Troubleshoot MCP and AI IAM access](../troubleshooting/mcp-ai-iam.md).

### Relationship with Access Authorization Events

[Section titled “Relationship with Access Authorization Events”](#relationship-with-access-authorization-events)

MCP Authorization Tracing and Access Authorization Events describe overlapping activity from different angles.

MCP Authorization Tracing is live and scoped to MCP authorization at MCP Identity Gateway. It shows the inbound request shape (redirect URI, resource, workload/policy match) before Aembit Cloud emits formal events.

Access Authorization Events are durable records of every authorization decision Aembit Cloud makes, available across all protocols. They include `access.request`, `access.authorization`, `access.discovery`, and `access.credential` event types with severity and full JSON payloads.
