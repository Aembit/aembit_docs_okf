---
type: how-to
title: "Review Workload Events"
description: "How to view and filter Workload Events in the Aembit Reporting Dashboard"
resource: https://docs.aembit.io/user-guide/audit-report/workload-events/review/
interface: web-ui
tags: ["workload-event", "audit-report"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Review Workload Events

The Reporting Dashboard shows the Workload Events that Agent Proxy generates as traffic flows between your Client Workloads and Server Workloads. For background on what these events contain, see [Workload Events](overview.md).

## View workload events

To see detailed information about workload events, follow these steps:

1. Log into your Aembit Tenant.

2. Click **Reporting**.

3. Click the **Workload Events** tab at the top of the Reporting Dashboard.

4. Click the filter icon to apply any filters or the refresh icon to retrieve the latest events.

The following filters are available:

| Filter                           | Description                                                                                                                                               |
| -------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Timespan**                     | The period of time you would like to have event data displayed.                                                                                           |
| **Application Protocols**        | The application protocol type to filter by.                                                                                                               |
| **Severity**                     | Filter by event severity: `Error`, `Warning`, or `Info`.                                                                                                  |
| **Server Workload Name**         | Filter by a specific Server Workload.                                                                                                                     |
| **Client Workload Name**         | Filter by a specific Client Workload.                                                                                                                     |
| **User (MCP App Protocol only)** | Filter MCP Workload Events to a specific user. Available only when filtering by `Application Protocol = MCP`. Matches the `application.mcp.userId` field. |

> **Event timing**
>
> Workload Events appear in near-real-time, but not instantly. After traffic flows through Agent Proxy, allow a short time for new events to show up in the dashboard.

## Event details

When you select a workload event from the dashboard, you can expand the view to display detailed data for that event.

![A workload event showing a summary panel on the left and full JSON output on the right](https://docs.aembit.io/_astro/reporting-workload-event-detail.BeuaZ7Wc_1kXO4S.webp)

The expanded view displays the event metadata, workload identification, network details, outcome, and protocol-specific application data. For a description of each field, see the [Workload Event reference](reference.md).
