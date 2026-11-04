---
type: how-to
title: "How to review MCP Access Activities"
description: "How to review MCP requests with no error or exception code in the Reporting dashboard"
resource: https://docs.aembit.io/user-guide/audit-report/mcp/access-activities/
interface: web-ui
tags: ["mcp", "audit-report"]
timestamp: 2026-11-03T15:14:28-05:00
---

# How to review MCP Access Activities

**MCP Access Activities** lists the MCP requests that passed through the MCP Identity Gateway without an error or an exception code. Each row is an activity, a group of requests that all have the same user, MCP client, MCP server, MCP method, and target. Requests that reached more than one MCP server share a **Multiple** row, whichever servers they reached.

MCP Access Activities also leaves out a request with an exception code of `Warning` severity. To review those requests, use [MCP Access Exceptions](access-exceptions.md). Only traffic from MCP Identity Gateway 1.35.6263 or later appears. See [MCP Identity Gateway supported versions](../../../reference/edge-components/edge-component-supported-versions.md#mcp-identity-gateway).

## What MCP Access Activities displays

MCP Access Activities shows times in Coordinated Universal Time (UTC). MCP Access Activities shows requests in the Resource Set you’re working in.

| Column              | Description                                                                  |
| ------------------- | ---------------------------------------------------------------------------- |
| **Last seen (UTC)** | The time of the most recent request in the activity.                         |
| **User**            | The user Aembit recorded for the request.                                    |
| **MCP client**      | The Client Workload that represents the MCP client.                          |
| **MCP server**      | The MCP server that the MCP Identity Gateway forwarded the request to.       |
| **MCP method**      | The MCP method the request called, such as `tools/call` or `resources/read`. |
| **Target**          | The tool name, resource URI, prompt name, or task ID the method acted on.    |
| **Requests**        | The number of requests in the activity in the selected timespan.             |

The table sorts by **Last seen (UTC)**, newest first.

When Aembit has no single value for a column, the cell shows one of these values:

| Value              | Column                         | Meaning                                                                                                                       |
| ------------------ | ------------------------------ | ----------------------------------------------------------------------------------------------------------------------------- |
| **Multiple**       | **MCP server**                 | Each request reached more than one MCP server, and requests that reached different servers share the row. Hover to list them. |
| **Not determined** | **MCP client**, **MCP server** | Aembit recorded no MCP client or no MCP server for the request.                                                               |
| **Not applicable** | **Target**                     | Aembit recorded no tool name, resource URI, prompt name, or task ID for the request.                                          |
| **Unavailable**    | **MCP method**                 | Aembit recorded no MCP method for the request.                                                                                |
| **Unknown**        | **User**                       | Aembit recorded no user for the request.                                                                                      |

## Review MCP Access Activities

Open the report and narrow it to the users, MCP clients, or MCP servers you’re investigating. Look for an unexpected MCP client, an MCP server a user shouldn’t reach, or a tool called more often than you expect.

1. Log into your Aembit Tenant.

2. Click **Reporting** in the left sidebar.

3. At the top, select **Reporting ☰ MCP Access Activities**.

   By default, Aembit displays the activities from the last 24 hours. When you return to the report, it keeps the timespan and filters you last set. If no requests match, the table shows “No MCP activity found.”

4. To shorten the window, select a value from the **Timespan** filter.

5. To narrow the table, use one or more filters:

   | Filter         | Matches                                                     |
   | -------------- | ----------------------------------------------------------- |
   | **User**       | Users whose recorded identifier contains the text you enter |
   | **MCP Client** | MCP clients whose name contains the text you enter          |
   | **MCP Server** | MCP servers whose name contains the text you enter          |
   | **MCP Method** | The MCP methods you select from the list                    |
   | **Target**     | Targets that contain the text you enter                     |

6. To search the **User**, **MCP client**, **MCP server**, **MCP method**, **Target**, and **Requests** columns at once, enter text in the search box.

## Review an activity’s requests

Open an activity to see each request in it. For a request you select, Aembit shows the Access Policy decisions behind it and the events it produced.

1. Click a row in MCP Access Activities.

   Aembit displays the **Activity Detail** page for that activity.

2. In **Activity summary**, review the activity’s values, including **First seen** and **Last seen**.

3. In **Request history**, find the request to review.

   Each row is one request, with its **Time (UTC)** and **HTTP response code**. The count beside the heading is the total number of requests, across all pages.

4. Click **View events** on the request.

   Aembit displays the sections for that request, described in the following table.

5. In **Relevant events**, click **Open event** to see an event’s raw JSON in the **Event details** panel.

After you select a request, the **Activity Detail** page shows these sections:

| Section                 | What it shows                                                                                                                  |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| **Access decisions**    | Each Access Policy decision for the request, with its **Boundary**, **Access Policy**, and **Decision**                        |
| **Per-server outcomes** | Each MCP server’s **Status**, **HTTP response code**, and **Duration (ms)**, when the request reached more than one MCP server |
| **Relevant events**     | The Workload Events and Access Authorization Events for the request                                                            |

MCP Access Activities and the **Activity Detail** page open only when your role has **Read Only** permission on **Workload Events**. **Access decisions** and the Access Authorization Events in **Relevant events** appear only when your role also has **Read Only** permission on **Access Authorization Events**. See [Roles](../../administration/roles/overview.md).

In **Access decisions**, **Boundary** shows **MCP client → Gateway access** or **Gateway → MCP server access**. **Decision** shows **Authorized**, **Denied**, or **Unavailable**. The section appears only when the request has at least one Access Policy decision.

When Aembit can’t find the event for an authorization the request references, **Access decisions** shows the **Access Authorization** boundary with **Unavailable** for **Access Policy** and **Decision**. **Relevant events** shows the same authorization with **Outside source window** as its time.

## Related pages

* [MCP Access Exceptions](access-exceptions.md)
* [MCP Authorization Tracing](authorization-tracing.md)
* [Workload Events](../workload-events/overview.md)
* [Access Authorization Events](../access-authorization-events.md)
* [MCP Identity Gateway](../../deploy-install/mcp-identity-gateway/overview.md)
