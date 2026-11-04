---
type: how-to
title: "How to review MCP Access Exceptions"
description: "How to review MCP requests that have an error or an exception code in the Reporting dashboard"
resource: https://docs.aembit.io/user-guide/audit-report/mcp/access-exceptions/
interface: web-ui
tags: ["mcp", "audit-report"]
timestamp: 2026-11-03T15:14:28-05:00
---

# How to review MCP Access Exceptions

**MCP Access Exceptions** lists the MCP requests that went through the MCP Identity Gateway with an error or an exception code. It groups requests that share a user, MCP client, MCP server, method, target, and exception, and counts the requests in each group. Requests that reached more than one MCP server share a **Multiple** row, whichever servers they reached. Use it to find which MCP requests had an error or an exception, for whom, and why. To review MCP requests with no error or exception code, use [MCP Access Activities](access-activities.md).

## What counts as an exception

A request is one MCP operation from an MCP client, such as one `tools/call`. Aembit groups every Workload Event the MCP Identity Gateway records for that operation into one request, including the events for each MCP server the request reaches.

A request counts as an exception when any of its events has severity `Error` or carries an exception code or an exception category. Exception codes with `Warning` severity count too, such as `AIDR_UNAVAILABLE_FAIL_OPEN_BYPASS` and `CONFLICTING_RESOURCE_URI`.

MCP Access Exceptions includes only requests through MCP Identity Gateway 1.35.6263 or later. See [MCP Identity Gateway supported versions](../../../reference/edge-components/edge-component-supported-versions.md#mcp-identity-gateway). The MCP Identity Gateway records no Workload Event for a request it rejects because its access token is missing, unverifiable, or expired, so MCP Access Exceptions doesn’t show it.

## What MCP Access Exceptions displays

Each row holds the requests that share every value from **User** to **Exception Code**. MCP Access Exceptions shows requests in the Resource Set you’re working in.

| Column             | Description                                                                                                                              |
| ------------------ | ---------------------------------------------------------------------------------------------------------------------------------------- |
| **Time (UTC)**     | The time of the most recent request in the row.                                                                                          |
| **User**           | The user Aembit recorded for the request.                                                                                                |
| **MCP Client**     | The name of the Client Workload that the MCP Identity Gateway identified as the MCP client. It’s not the name of the MCP client product. |
| **MCP Server**     | The MCP server the MCP Identity Gateway forwarded the request to.                                                                        |
| **MCP Method**     | The MCP method, such as `tools/call` or `resources/read`.                                                                                |
| **Target**         | The tool name, resource URI, prompt name, or task ID that the request names.                                                             |
| **Category**       | The exception category of one event in the request, such as **MCP Server**.                                                              |
| **Exception Code** | The label of that same event’s exception code, such as **Invalid Audience**. Hover over the label to see the code’s message.             |
| **Requests**       | The number of requests in the row.                                                                                                       |

A request can have more than one event with an error or an exception. The row then shows the category and code of the most recent event that carries an exception code. When none carries a code, the row uses the most recent event that carries a category.

When a column has no value for a request, it shows one of these instead:

| Value              | Column             | Meaning                                                                                                                                         |
| ------------------ | ------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| **Unknown**        | **User**           | The request’s events don’t name a user.                                                                                                         |
| **Unknown**        | **Category**       | The event the row describes carries no exception category.                                                                                      |
| **Unknown**        | **Exception Code** | The event the row describes carries no exception code.                                                                                          |
| **Unavailable**    | **MCP Method**     | The request’s events don’t name the MCP method.                                                                                                 |
| **Not applicable** | **Target**         | Aembit recorded no tool name, resource URI, prompt name, or task ID for the request.                                                            |
| **Not determined** | **MCP Client**     | The request’s events don’t name a Client Workload.                                                                                              |
| **Not determined** | **MCP Server**     | The MCP Identity Gateway didn’t forward the request to an MCP server.                                                                           |
| **Multiple**       | **MCP Server**     | Each request reached more than one MCP server, and requests that reached different servers share the row. Hover over **Multiple** to list them. |

## Review MCP Access Exceptions

1. Log into your Aembit Tenant.

2. Click **Reporting** in the left sidebar.

3. At the top, select **Reporting ☰ MCP Access Exceptions**.

   Aembit displays the **MCP Access Exceptions** page, with the rows that have the most requests first. By default, it shows requests from the last 24 hours. When you return to the report, it keeps the timespan and filters you last set.

4. Use any of these controls to narrow the list:

   | Control            | What it does                                                                                    |
   | ------------------ | ----------------------------------------------------------------------------------------------- |
   | **Timespan**       | Sets a period within the last 24 hours                                                          |
   | **User**           | Matches part of the user identifier Aembit recorded                                             |
   | **MCP Client**     | Matches part of a value                                                                         |
   | **MCP Server**     | Matches part of a value, and keeps a **Multiple** row when one of its MCP servers matches       |
   | **MCP Method**     | Accepts more than one value                                                                     |
   | **Target**         | Matches part of a value                                                                         |
   | **Category**       | Accepts more than one value, and limits the **Exception Code** options to the codes it contains |
   | **Exception Code** | Accepts more than one value                                                                     |
   | Search box         | Searches every column except **Time (UTC)** at once                                             |

   Hover over a value in **Exception Code** to see its message. For every code and its message, see [MCP exception codes](exception-codes.md).

## Review the requests in a row

Open a row to see each request in it. For a request you select, Aembit shows its error or exception and the events it produced. It also shows the Access Policy decisions behind the request.

1. Click a row in MCP Access Exceptions.

   Aembit displays the **Exception Detail** page for that row.

2. In **Exception summary**, review the row’s values, including **First seen**, **Last seen**, **Category**, and **Exception code**.

3. In **Request history**, find the request to review.

   Each row is one request, with its **Time (UTC)**, **Result**, **HTTP response code**, and **Server reached**. The count beside the heading is the total number of requests, across all pages.

4. Click **View events** on the request.

   Aembit updates **Exception summary** for that request and displays the sections described in the following tables.

5. In **Relevant events**, click **Open event** to see an event’s raw JSON in the **Event details** panel.

After you select a request, these **Exception summary** fields describe that request:

| Field                      | What it shows                                                                                                                     |
| -------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| **Result**                 | **Error** when an event in the request has `Error` severity, and **Success** otherwise                                            |
| **HTTP response code**     | The HTTP response code for the request                                                                                            |
| **Raw reason**             | The reason Aembit recorded for the error or exception, or the exception code’s message when Aembit recorded no reason             |
| **Client access decision** | The decision for the MCP client’s access to the MCP Identity Gateway, with its Access Policy. It appears only when Aembit has one |

**Result** shows **Success** when none of the request’s events has `Error` severity, even though the request carries an exception code.

The **Exception Detail** page also shows these sections for the selected request:

| Section                 | What it shows                                                                                                                                                             |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Access decisions**    | Each Access Policy decision for the request, with its **Boundary**, **Access Policy**, and **Decision**                                                                   |
| **Per-server outcomes** | Each MCP server’s **Status**, **HTTP code**, **Category**, **Exception code**, **Raw reason**, and **Duration (ms)**, when the MCP Identity Gateway forwarded the request |
| **Relevant events**     | The Workload Events and Access Authorization Events for the request                                                                                                       |

MCP Access Exceptions shows requests, and the **Exception Detail** page opens, only when your role has **Read Only** permission on **Workload Events**. **Access decisions**, **Client access decision**, and the Access Authorization Events in **Relevant events** appear only when your role also has **Read Only** permission on **Access Authorization Events**. See [Roles](../../administration/roles/overview.md).

In **Access decisions**, **Boundary** shows **MCP client → Gateway access** or **Gateway → MCP server access**.

**Decision** shows **Authorized**, **Denied**, or **Unavailable**. The section appears only when the request has at least one Access Policy decision.

When Aembit can’t find the event for an authorization the request references, **Access decisions** shows the **Access Authorization** boundary with **Unavailable** for **Access Policy** and **Decision**. **Relevant events** shows the same authorization with **Outside source window** as its time.

## Related pages

* [MCP Access Activities](access-activities.md)
* [MCP Authorization Tracing](authorization-tracing.md)
* [Workload Events](../workload-events/overview.md)
* [Troubleshoot MCP and AI IAM access](../../troubleshooting/mcp-ai-iam.md)
