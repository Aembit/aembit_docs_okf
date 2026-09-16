---
type: reference
title: "MCP Tool Access Control tool name reference"
description: "Glob syntax and field limits for the tool names you add to MCP Tool Access Control."
resource: https://docs.aembit.io/user-guide/access-policies/content-security/mcp-tool-access-control/reference/
interface: web-ui
tags: ["mcp-tool-access-control", "content-security", "access-policy"]
timestamp: 2026-09-16T12:05:53-04:00
---

# MCP Tool Access Control tool name reference

MCP Tool Access Control matches the tools you add against the names an Model Context Protocol (MCP) server publishes. You can add an exact MCP tool name or a glob pattern. Both the Tool Visibility and Tool Invocation controls use that syntax. This page covers that syntax and the field limits.

To learn more about MCP Tool Access Control, see [MCP Tool Access Control](overview.md). To add MCP tools to an Access Policy, see [Add MCP Tool Access Control to an Access Policy](add-to-policy.md).

## MCP tool name matching

A name with no glob characters matches one tool exactly. Matching is case-sensitive. `addComment` and `addcomment` name different tools, so adding one doesn’t cover the other.

> **Aembit matches the name the MCP server publishes**
>
> When an MCP Client requests `tools/list`, the MCP Identity Gateway prefixes each MCP tool name with the Server Workload it came from, so that names from different MCP servers can’t collide.
>
> Aembit matches the name the upstream MCP Server publishes, not the prefixed name the client receives, so a name copied out of an AI agent’s MCP tool list carries a prefix that doesn’t match. Take MCP tool names from the MCP server’s documentation. See [Proxied MCP methods](../../../deploy-install/mcp-identity-gateway/reference-mcp-gateway.md#proxied-mcp-methods).

## Glob patterns

| Pattern  | Matches                                                           |
| -------- | ----------------------------------------------------------------- |
| `*`      | Any run of characters.                                            |
| `?`      | Exactly one character.                                            |
| `[set]`  | One character from a set, such as `[abc]`.                        |
| `[a-z]`  | One character from a range.                                       |
| `[!set]` | One character that isn’t in a set or range, such as `[!0-9]`.     |
| `[*]`    | A literal `*`. Brackets escape a glob character.                  |
| `**`     | Every MCP tool name. Valid only as the whole pattern, on its own. |

### Pattern examples

| Pattern         | Matches                 | Doesn’t match  |
| --------------- | ----------------------- | -------------- |
| `get_*`         | `get_issue`, `get_user` | `list_issues`  |
| `delete_?`      | `delete_1`              | `delete_issue` |
| `[gs]et_user`   | `get_user`, `set_user`  | `let_user`     |
| `admin_[!0-9]*` | `admin_settings`        | `admin_2fa`    |

### Unsupported syntax

| Not supported                  | Use instead           |
| ------------------------------ | --------------------- |
| `{get,list}_*` brace expansion | One pattern per entry |
| `[^0-9]` caret negation        | `[!0-9]`              |
| `get**user` embedded `**`      | `get*user`            |

The Aembit Tenant UI validates each entry as you type and rejects an invalid pattern with this message:

`Letters, numbers, _, -, *, ?, ! and glob character classes (e.g. [a-z], [0-9]) only`

Dots and slashes aren’t accepted, so an entry can’t match an MCP tool published as `admin.tools.list`.

## MCP tool name limits

Each **MCP Tool Name** entry accepts up to 128 characters.
