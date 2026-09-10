---
type: how-to
title: "Connect with GitHub Copilot"
description: "Configure GitHub Copilot to query Aembit event logs through the MCP Server."
resource: https://docs.aembit.io/ai-guide/mcp/mcp-server/connect/github-copilot/
interface: mcp
tags: ["connect", "mcp-server", "mcp"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Connect with GitHub Copilot

Connect GitHub Copilot’s agent mode to the Aembit MCP Server to query audit logs and events directly from your development environment.

## Prerequisites

* GitHub Copilot subscription with agent mode access
* VS Code or compatible IDE with Copilot extension
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

3. Reload your IDE to pick up the new configuration.

## Verify the connection

When Copilot detects MCP tools, it may prompt you for permission to use them. Select **Allow** (once or always) to enable Aembit queries.

Try asking Copilot:

* “What Aembit MCP tools are available?”
* “Show recent audit logs from Aembit.”

## Usage notes

Keep these points in mind when using GitHub Copilot with the Aembit MCP Server.

* Copilot translates natural language into structured MCP queries.
* The MCP Server expects precise, structured commands—if Copilot’s query fails, try being more specific.
* The MCP Server only supports read operations; you can’t modify Aembit data through this connection.

## Next steps

* See the [Prompt Library](../../../prompt-library/overview.md) for example prompts and best practices.
* If you have connection issues, see [Troubleshooting](overview.md#troubleshooting).

## Learn more

* [GitHub Copilot MCP documentation](https://docs.github.com/en/copilot/using-github-copilot/using-extensions-to-integrate-external-tools-with-copilot-chat/using-model-context-protocol-with-github-copilot)
