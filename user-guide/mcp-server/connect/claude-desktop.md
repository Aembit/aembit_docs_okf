---
type: how-to
title: "Connect with Claude (Desktop/web)"
description: "Configure Claude Desktop or Claude on the web to query Aembit event logs through the MCP Server."
resource: https://docs.aembit.io/user-guide/mcp-server/connect/claude-desktop/
interface: mcp
tags: ["connect", "mcp-server"]
timestamp: 2026-09-15T18:18:13-07:00
---

# Connect with Claude (Desktop/web)

Connect Claude Desktop or Claude on the web to the Aembit MCP Server to query audit logs, authorization events, and workload events from your Aembit Tenant.

## Choose a connection method

This page covers two ways to connect Claude to Aembit’s MCP Server. Pick the method that fits your situation:

| Method                                                                              | When to use                                                                                                                                                 |
| ----------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**Connectors UI**](#claude-connectors-ui-recommended)                              | **OAuth with single sign-on (SSO)** Day-to-day use, multiple users, or long-lived access without token rotation                                             |
| [**Local configuration**](#local-configuration-with-stdio-http-bridge-desktop-only) | **Static API Token with stdio-to-HTTP bridge** Quick one-off testing, automation that can’t complete an interactive sign-in, or environments without an IdP |

The Connectors UI method works in both Claude Desktop and Claude on the web. The local configuration method is **Claude Desktop only**, as Claude on the web doesn’t read a local config file.

## Claude Connectors UI (recommended)

Aembit’s Access Policy gates the MCP Server. Claude registers as an OAuth client through Aembit, authenticates the user against your IdP, and then receives an Aembit Access Token tied to that user’s identity. Aembit refreshes the token as long as the user stays signed in, so there’s nothing for the user to rotate in Claude.

### Prerequisites

* Claude Desktop installed ([download](https://claude.ai/download)), or access to Claude on the web ([claude.ai](https://claude.ai/))
* **MCP Service URL** from the Aembit Admin UI Profile screen (see [Connect to the MCP Server](overview.md)) in the following format: `<tenantId>.mcp.useast2.aembit.io`
* An Aembit role that can create Credential Providers, Client and Server Workloads, and Access Policies
* An IdP configured in your Aembit Tenant under [Administrators > Identity Providers](../../administration/identity-providers/overview.md)

### Create an Access Policy for Claude Desktop/web

Follow these steps to create an Access Policy that allows Claude to authenticate and access the Aembit MCP Server:

1. Log into your Aembit Tenant and go to Access Policies, then click **+ New**.

2. Name the Access Policy something like “Claude Connector” and optionally add a description.

3. Click **Save**. If you need full details about creating this Access Policy, see [Create an Access Policy](../../access-policies/create-access-policy.md).

4. Add a **Client Workload** to represent Claude with these values:

   | Field                   | Value                 |
   | ----------------------- | --------------------- |
   | Client identifier type  | `Redirect URI`        |
   | Client identifier value | `https://claude.ai/*` |
   | Enforce SSO             | Enabled               |
   | Identity provider       | Your configured IdP   |

   The trailing `/*` on the **Redirect URI** lets a single Client Workload cover both Claude Desktop and Claude on the web. If you don’t have an IdP configured yet, see [Identity Providers](../../administration/identity-providers/overview.md) to add one before continuing.

   If you need the full procedure, see [Client Workloads](../../access-policies/client-workloads/overview.md).

5. Add a **Server Workload** for the *Aembit MCP Server* with these values:

   | Field                   | Value                              |
   | ----------------------- | ---------------------------------- |
   | Authentication protocol | `MCP`                              |
   | Host                    | `<tenantId>.mcp.useast2.aembit.io` |
   | Port                    | `443`                              |
   | URL path                | `/mcp`                             |

   Find your tenant’s MCP Service URL on the Aembit Admin UI Profile screen, or at the bottom of the **Authentication** section. Make sure `/mcp` appears in the **URL path** field—without it, the connector returns a 502 from the Aembit gateway.

   If you need the full procedure, see [Server Workloads](../../access-policies/server-workloads/overview.md).

6. Add a **Credential Provider** for your *Aembit Access Token* with the following values:

   | Field                        | Value                                                                                     |
   | ---------------------------- | ----------------------------------------------------------------------------------------- |
   | Credential Type              | `Aembit Access Token`                                                                     |
   | Role                         | Select the desired role you want this credential to provide                               |
   | Lifetime                     | Select the desired lifetime of this token (default is `900` seconds)                      |
   | Enable Refresh Token Support | Optionally set this value if you want the token to automatically refresh after a duration |

   If you need the full procedure, see [Configure an Aembit Access Token Credential Provider](../../access-policies/credential-providers/aembit-access-token.md).

7. Save and Activate the Access Policy.

### Connect Claude to Aembit

1. Open Claude (Desktop or on the web), then in the left sidebar menu, go to **Customize** -> **Connectors**.

2. In the **Connectors** sidebar panel, click **+ Add Connector** and select **… Add custom connector**.

   > **About permissions**
   >
   > If the **… Add custom connector** option isn’t available, your account may not have permission to create connectors. Contact your administrator to either have them create the connector for you or grant you the necessary permissions (the `connector.create` permission).

3. Enter the desired **Name**.

4. Enter the **Remove MCP Server URL** of your full MCP Service URL (for example, `https://<tenantId>.mcp.useast2.aembit.io/mcp`) and continue.

5. Claude redirects you to your IdP, and sign in with the same account you used to log into the Aembit Admin UI and set up the Access Policy. After successful authentication, your IdP directs you back to Claude.

6. After the IdP returns you to Claude, the Aembit connector appears as **Connected** in the Connectors list.

### Verify the connection

1. Expand the Aembit connector under `Settings` > `Connectors`. The list of available tools appears, including `get_audit_logs`, `get_audit_events`, and `get_workload_events`.

2. In a new chat, ask Claude a question such as **“Show me the last 10 audit log entries.”** Claude routes the request through the Aembit MCP Server and returns results.

## Local configuration with stdio-HTTP bridge (Desktop only)

Use this method when your Aembit tenant doesn’t have an IdP configured yet, or for a one-off test before you set up the full [Connectors UI method](#claude-connectors-ui-recommended).

> **About the stdio-HTTP bridge**
>
> Claude Desktop’s local configuration only supports stdio-based MCP servers. Anthropic routes remote MCP servers through the [Connectors UI](#claude-connectors-ui-recommended) instead. The stdio-HTTP bridge works around that by relaying Claude Desktop’s stdio traffic through [`mcp-remote`](https://github.com/geelen/mcp-remote), an MIT-licensed community bridge. Neither Anthropic nor the Model Context Protocol project publishes an official equivalent. Aembit doesn’t maintain `mcp-remote`; evaluate it like any other third-party dependency before relying on it in production.

### How the stdio-HTTP bridge works

Claude Desktop launches `mcp-remote` as a local bridge. The `mcp-remote` bridge forwards Claude’s stdio MCP traffic to the Aembit MCP Server over Streamable HTTP and attaches your Aembit API Token to each request as a Bearer credential.

### Prerequisites

* Claude Desktop app installed ([download](https://claude.ai/download))
* **Node.js installed on the operating system Claude Desktop runs on** (not inside Windows Subsystem for Linux), with `npx` available on the system PATH. Verify with `where.exe npx` (Windows) or `which npx` (macOS) from a new terminal—both should return a path. See [Troubleshooting](#troubleshooting) if they don’t.
* Aembit API Token and MCP Service URL from the Aembit Admin UI Profile screen (see [Connect to the MCP Server](overview.md))

### Add Aembit’s MCP server to `claude_desktop_config.json`

> **Protect your API Token**
>
> Avoid committing API tokens to version control. Store tokens in environment variables or a secrets manager, and reference them in your configuration file.

1. Locate the `claude_desktop_config.json` file on your system:

   | Operating System | Path                                                              |
   | ---------------- | ----------------------------------------------------------------- |
   | macOS            | `~/Library/Application Support/Claude/claude_desktop_config.json` |
   | Windows          | `%APPDATA%\Claude\claude_desktop_config.json`                     |

   Create the file if it doesn’t exist.

2. Add the Aembit MCP server configuration to `claude_desktop_config.json`, replacing the following values:

   * `<tenantId>` with your tenant ID from the Aembit Admin UI Profile screen
   * `<your-aembit-api-token>` with your Aembit API Token from the Profile screen

   - macOS

     **claude\_desktop\_config.json**

     ```json
     {
       "mcpServers": {
         "aembit": {
           "command": "npx",
           "args": [
             "-y",
             "mcp-remote",
             "https://<tenantId>.mcp.useast2.aembit.io/mcp",
             "--header",
             "Authorization:Bearer ${AEMBIT_API_TOKEN}"
           ],
           "env": {
             "AEMBIT_API_TOKEN": "<your-aembit-api-token>"
           }
         }
       }
     }
     ```

   - Windows

     **claude\_desktop\_config.json**

     ```json
     {
       "mcpServers": {
         "aembit": {
           "command": "npx.cmd",
           "args": [
             "-y",
             "mcp-remote",
             "https://<tenantId>.mcp.useast2.aembit.io/mcp",
             "--header",
             "Authorization:Bearer ${AEMBIT_API_TOKEN}"
           ],
           "env": {
             "AEMBIT_API_TOKEN": "<your-aembit-api-token>"
           }
         }
       }
     }
     ```

     Notice on Windows that you must use `npx.cmd` instead of `npx` so Claude Desktop’s process spawner resolves the file extension correctly.

   > **Don’t add a space after `Authorization:`**
   >
   > Claude Desktop on Windows and Cursor have a known bug where spaces inside `npx` arguments get mangled. Write the header value as `Authorization:Bearer ${AEMBIT_API_TOKEN}` with no space around the colon. Spaces inside `env` values work fine—the bug only affects values inside `args`.

3. Fully restart Claude Desktop to load the new configuration.

   * macOS

     Quit Claude Desktop from the menu bar (`Claude` > `Quit Claude`, or `Cmd+Q`), then reopen.

   * Windows

     > **Closing the Claude Desktop window isn’t enough**
     >
     > On Windows, closing the Claude Desktop window sends the app to the system tray, and the process keeps running with the old in-memory configuration. You must **fully quit** the app for `claude_desktop_config.json` changes to take effect.
     >
     > 1. Find the Claude icon in the system tray (you may need to expand the **Show hidden icons** arrow).
     > 2. Right-click the icon and choose `Quit`.
     > 3. Open `Task Manager` and confirm no `Claude.exe` processes remain.
     > 4. Relaunch Claude Desktop from the Start menu.

### Verify the connection

Verify that Claude Desktop connects to the Aembit MCP Server and can query data.

1. Open Claude Desktop.

2. Open `Settings` and locate the Aembit MCP server in the MCP servers list.

   > **Note**
   >
   > Locally configured MCP servers don’t appear under `Settings` > `Connectors`, which Claude Desktop reserves for OAuth-based servers. Look under `Developer` > `MCP Servers` or under an `MCP Servers` top-level item, depending on your Claude Desktop version.

3. Confirm the Aembit server appears in the list and reports as connected. If it shows as failed, see [Troubleshooting](#troubleshooting).

4. In a new chat, try a prompt like: “List the available Aembit MCP tools.”

## Example prompts

After connecting, you can ask Claude questions like:

* “Show me the last 10 audit log entries.”
* “What authorization events occurred in the past hour?”
* “List workload events for the api-gateway service.”

Claude translates your questions into structured MCP queries automatically. See the [Prompt Library](../prompt-library/overview.md) for example prompts and best practices.

## Troubleshooting

The following sections cover failure modes for the **local configuration with stdio-HTTP bridge** method. When this method fails, Claude Desktop writes errors to a per-server log file. Locate the log first, then match the error against the relevant section.

### Find the log file

| Operating System | Path                                          |
| ---------------- | --------------------------------------------- |
| macOS            | `~/Library/Logs/Claude/mcp-server-aembit.log` |
| Windows          | `%APPDATA%\Claude\logs\mcp-server-aembit.log` |

Claude Desktop creates this file the first time it spawns the `aembit` MCP server. If the file doesn’t exist after you’ve added the configuration and restarted Claude Desktop, see [The Aembit server doesn’t appear in Claude Desktop](#the-aembit-server-doesnt-appear-in-claude-desktop).

### `spawn npx ENOENT` or `'npx' is not recognized`

**Symptom:** The log contains one of:

```text
'npx' is not recognized as an internal or external command, operable program or batch file.
Error: spawn npx ENOENT
```

**Cause:** Node.js isn’t installed on the system PATH that Claude Desktop inherits. On Windows, this commonly happens for two reasons. You may have installed Node only inside Windows Subsystem for Linux (WSL), which is invisible to Windows GUI applications. Or you installed Node through a version manager such as `nvm-windows` or `fnm` that only updates PATH for shell sessions.

**Fix:**

1. Install Node.js on the operating system Claude Desktop runs on. On Windows, use the official installer from [nodejs.org](https://nodejs.org/), Chocolatey (`choco install nodejs-lts -y`), or `winget` (`winget install OpenJS.NodeJS.LTS`).
2. Open a **new** terminal and confirm `npx` resolves: `where.exe npx` on Windows or `which npx` on macOS. You should see a path such as `C:\Program Files\nodejs\npx.cmd`.
3. Fully quit and relaunch Claude Desktop (see step 3 of [Add Aembit’s MCP server](#add-aembits-mcp-server-to-claude_desktop_configjson)). Claude Desktop inherits PATH at launch; a running instance won’t see the new install.

### Error: `502 Bad Gateway`

**Symptom:** The log contains:

```text
Connection error: StreamableHTTPError: Streamable HTTP error: Error POSTing to endpoint: <html>
<head><title>502 Bad Gateway</title></head>
```

**Cause:** The MCP Service URL in your configuration doesn’t match a healthy backend. The most common reason is a stack mismatch—Aembit tenants live on Aembit-managed stacks (such as `useast2` and `usd1`), and each stack uses a different hostname. If the URL in your config points at a stack where your tenant doesn’t exist, the Aembit gateway has nothing to route the request to and returns 502.

**Fix:**

1. Open the Aembit Admin UI Profile screen and copy the **exact** MCP Service URL shown there, including the stack segment.
2. Update the URL in `claude_desktop_config.json` to match.
3. If your Aembit API Token has expired, generate a fresh one from the Profile screen. The default token lifetime is 1 hour, and you can choose a longer value when generating the token.
4. Fully quit and relaunch Claude Desktop.

Less common 502 causes: the MCP backend is temporarily unhealthy for your tenant, or the MCP feature isn’t provisioned for your tenant (which more often returns 403). If you’ve confirmed the URL and the issue persists, contact your Aembit administrator.

### Error: `401 Unauthorized`

**Symptom:** mcp-remote connects, but every request returns 401.

**Cause:** Your Aembit API Token is invalid or has expired. Profile-screen tokens have a default 1-hour lifetime (extendable) and must still be valid when Claude Desktop spawns mcp-remote and makes its first request.

**Fix:** Generate a fresh API Token from the Profile screen, update `AEMBIT_API_TOKEN` in `claude_desktop_config.json`, and fully quit and relaunch Claude Desktop.

### The Aembit server doesn’t appear in Claude Desktop

If `mcp-server-aembit.log` doesn’t exist after you’ve added the configuration and restarted, Claude Desktop hasn’t attempted to spawn the server. Check each of these:

* **Are you looking in the right section?** Locally configured servers don’t appear under `Settings` > `Connectors` (OAuth-only). Look under `Developer` > `MCP Servers` or under an `MCP Servers` top-level Settings item.
* **Did you fully quit Claude Desktop?** On Windows, closing the window sends the app to the system tray; the process keeps running with the old configuration in memory. Right-click the tray icon and choose `Quit`, then relaunch.
* **Is your `claude_desktop_config.json` valid JSON?** A syntax error elsewhere in the file can cause Claude Desktop to skip MCP server initialization. Validate the file with a JSON linter.

## Next steps

* See the [Prompt Library](../prompt-library/overview.md) for example prompts and best practices.
* If you have connection issues, see the preceding [Troubleshooting](#troubleshooting) section or the [overview page](overview.md#troubleshooting).
