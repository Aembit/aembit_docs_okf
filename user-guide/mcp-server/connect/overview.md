---
type: how-to
title: "Connect to the MCP Server"
description: "Prerequisites and authentication for connecting your AI tools to the Aembit MCP Server."
resource: https://docs.aembit.io/user-guide/mcp-server/connect/
interface: mcp
tags: ["connect", "mcp-server"]
timestamp: 2026-09-15T18:18:13-07:00
---

# Connect to the MCP Server

Use this page to find authentication requirements and connection instructions for the Aembit MCP Server.

> **Read-only access**
>
> The Aembit MCP Server only supports read operations. You can’t create, update, or delete data through the MCP Server.

## Prerequisites

Before connecting any client, ensure you have:

* **Feature enabled:** Your Aembit administrator must enable the MCP Server for your tenant. If the feature isn’t enabled, you receive a 403 error when attempting to connect.

* **API Token:** Generate or retrieve your API Token from the Aembit Admin UI:

  1. Log in to the Aembit Admin UI.
  2. Navigate to your Profile page.
  3. Generate or copy your API Token.

* **MCP Service URL:** Find your MCP Service URL in the Aembit Admin UI Profile screen.

## MCP service URL

Your MCP Service URL follows this pattern:

```shell
https://<tenantId>.mcp.useast2.aembit.io/mcp
```

Replace `<tenantId>` with your tenant identifier (visible in your Admin UI Profile screen).

## Authentication

All connections require a Bearer token in the `Authorization` header:

```shell
Authorization: Bearer <apiToken>
```

### Resource set scoping Optional

By default, queries use your default resource set. To scope queries to a different resource set, include:

```shell
X-Aembit-ResourceSet: <resourceSetId>
```

Resource sets provide separation of concerns, allowing you to segment event data access by team, environment, or project. [Learn more about Resource Sets](../../administration/resource-sets/overview.md).

## Choose your client

Select your AI tool to get connection instructions:

* [Claude Desktop](claude-desktop.md) - Connect the Claude desktop app to query Aembit events
* [MCP Inspector](mcp-inspector.md) - Test and explore the MCP endpoint interactively
* [GitHub Copilot](github-copilot.md) - Integrate with GitHub Copilot’s agent mode
* [Visual Studio](visual-studio.md) - Connect from Visual Studio’s MCP support

## Troubleshooting

The following sections help resolve common connection, authentication, and query issues:

### Connection issues

**Server not detected or not appearing:**

* Verify your configuration file exists in the correct location.
* Check that the JSON syntax is valid (no trailing commas, proper quotes).
* Restart your IDE or application fully.

**Connection timeout:**

* Verify your endpoint URL matches the format shown in your Admin UI Profile screen.
* Check that the transport type is correct for your client (Streamable HTTP for MCP Inspector).

### Authentication errors

**403 Forbidden error:**

* Your administrator hasn’t enabled the MCP Server for your tenant.
* Your API token is invalid or expired.
* Generate a new token from the Aembit Admin UI Profile page.

**Permission denied:**

* Confirm your API token is correct and hasn’t expired.
* Verify that you have enabled the MCP Server feature for your tenant. Contact your Aembit administrator if needed.

### Query issues

**No data returned or empty results:**

* Adjust your filter parameters (try a broader time range).
* Verify you’re querying the correct resource set.
* Check that the resource set contains data for the period.

**“Tool not found” errors:**

* You may need to enable the MCP Server for your Aembit Tenant.
* Contact your Aembit administrator to verify feature enablement.

## Prompts and examples

For example prompts and prompt engineering best practices, see the [Prompt Library](../prompt-library/overview.md).
