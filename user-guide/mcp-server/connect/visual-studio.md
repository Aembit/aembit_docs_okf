---
type: how-to
title: "Connect with Visual Studio"
description: "Configure Visual Studio to query Aembit event logs through the MCP Server."
resource: https://docs.aembit.io/user-guide/mcp-server/connect/visual-studio/
interface: mcp
tags: ["connect", "mcp-server"]
timestamp: 2026-09-15T18:18:13-07:00
---

# Connect with Visual Studio

Connect Visual Studio’s MCP support to the Aembit MCP Server to query audit logs and events directly from your IDE.

## Prerequisites

* Visual Studio 2022 or later with MCP support
* Aembit API Token and MCP Service URL (see [Connect to the MCP Server](overview.md))

## Configure the MCP connection

> **Protect your API Token**
>
> Avoid committing API tokens to version control. Store tokens in environment variables or a secrets manager, and reference them in your configuration file.

1. Create the configuration file.

   In your project root (or user config directory), create a file named `.mcp.json`:

   ```json
   {
     "servers": {
       "aembit": {
         "url": "https://<tenantId>.mcp.useast2.aembit.io/mcp",
         "headers": {
           "Authorization": "Bearer <apiToken>"
         }
       }
     }
   }
   ```

   Replace:

   * `<tenantId>` with your tenant identifier from the Aembit Admin UI
   * `<apiToken>` with your API Token

   Add `.mcp.json` to your `.gitignore` file to prevent committing it to version control.

2. (Optional) Scope to a resource set.

   Add the `X-Aembit-ResourceSet` header to query a specific resource set:

   ```json
   {
     "servers": {
       "aembit": {
         "url": "https://<tenantId>.mcp.useast2.aembit.io/mcp",
         "headers": {
           "Authorization": "Bearer <apiToken>",
           "X-Aembit-ResourceSet": "<resourceSetId>"
         }
       }
     }
   }
   ```

3. Restart Visual Studio to load the configuration.

## Verify the connection

Visual Studio may prompt for permission when MCP tools are first invoked. Select **Allow** to enable Aembit queries.

Test the connection by asking about available tools or requesting recent audit logs.

## Usage notes

Keep these points in mind when using Visual Studio’s MCP support with the Aembit MCP Server.

* The MCP Server expects structured JSON commands, not free-form natural language.
* The MCP Server only supports read operations; you can’t modify Aembit data through this connection.
* Queries operate on your default resource set unless you specify `X-Aembit-ResourceSet`.

## Next steps

* See the [Prompt Library](../prompt-library/overview.md) for example prompts and best practices.
* If you have connection issues, see [Troubleshooting](overview.md#troubleshooting).

## Learn more

* [Visual Studio MCP documentation](https://learn.microsoft.com/en-us/visualstudio/ide/mcp-servers)
