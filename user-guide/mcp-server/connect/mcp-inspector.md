---
type: how-to
title: "Connect with MCP Inspector"
description: "Use MCP Inspector to test and explore the Aembit MCP Server interactively."
resource: https://docs.aembit.io/user-guide/mcp-server/connect/mcp-inspector/
interface: mcp
tags: ["connect", "mcp-server"]
timestamp: 2026-09-15T18:18:13-07:00
---

# Connect with MCP Inspector

MCP Inspector is a visual testing tool for MCP endpoints—similar to Postman for REST APIs. Use it to explore available tools, test queries, and verify your connection before integrating with other clients.

## Prerequisites

* Node.js installed ([download](https://nodejs.org/))
* Aembit API Token and MCP Service URL (see [Connect to the MCP Server](overview.md))

## Launch MCP inspector

Run the following command to download and launch MCP Inspector:

```shell
npx @modelcontextprotocol/inspector
```

Your browser opens automatically to the MCP Inspector interface.

> **Tip**
>
> You can also use the hosted version at [mcpjam.com](https://www.mcpjam.com/).

## Configure the connection

To connect MCP Inspector to the Aembit MCP Server, configure the transport type, endpoint URL, and authentication.

1. Set the transport type.

   Select **Streamable HTTP** from the Transport Type dropdown.

2. Enter your MCP Service URL.

   Paste your Aembit MCP Service URL:

   ```shell
   https://<tenantId>.mcp.useast2.aembit.io/mcp
   ```

3. Set the connection type.

   Select **Via Proxy**.

4. Configure authentication.

   * Enable **Custom Header**.
   * Set the header name to `Authorization`.
   * Set the value to `Bearer <apiToken>` (include “Bearer ” with a space before the token). Replace `<apiToken>` with your API Token from the Aembit Admin UI.

5. Click **Connect**.

## Explore and test

Once connected:

1. Go to the **Tools** tab.
2. Click **List Tools** to see available Aembit MCP tools.
3. Select a tool (like `get_audit_logs`) from the list.
4. Configure parameters in the right pane.
5. Click **Run Tool** to execute the query and view results.

## Example: Query audit logs

1. Select the `get_audit_logs` tool.

2. Set parameters:

   ```json
   {
     "filter": {
       "timestamp": { "gte": "2025-01-01T00:00:00Z" }
     },
     "pagination": { "limit": 10 }
   }
   ```

3. Click **Run Tool**.

4. View the returned audit log entries in the response pane.

## Next steps

* See the [Prompt Library](../prompt-library/overview.md) for example prompts and best practices.
* If you have connection issues, see [Troubleshooting](overview.md#troubleshooting).
