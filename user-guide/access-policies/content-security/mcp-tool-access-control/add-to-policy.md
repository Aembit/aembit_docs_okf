---
type: how-to
title: "Add MCP Tool Access Control Content Security"
description: "How to add Content Security with MCP Tool Access Control to an Access Policy in the Access Policy Builder."
resource: https://docs.aembit.io/user-guide/access-policies/content-security/mcp-tool-access-control/add-to-policy/
interface: web-ui
tags: ["mcp-tool-access-control", "content-security", "access-policy"]
timestamp: 2026-09-16T11:10:21-07:00
---

# Add MCP Tool Access Control Content Security

This guide shows how to add an MCP Tool Access Control Content Security Provider to an Access Policy in the Access Policy Builder.

Add a Content Security Provider to each Access Policy you want to control. Each Access Policy then governs its own tools.

To learn more about MCP Tool Access Control, see [MCP Tool Access Control](overview.md). For the syntax a tool name or pattern accepts, see [MCP Tool Access Control tool name reference](reference.md).

> **Note**
>
> MCP Tool Access Control isn’t enabled by default. Contact [Aembit support](https://aembit.io/contact) to enable this feature.

## Prerequisites

* Access to the Aembit Tenant UI with permission to create and edit Access Policies. See [Create an Access Policy](../../create-access-policy.md).
* An Access Policy whose Server Workload is the MCP server you want to control, reached through the MCP Identity Gateway.
* The names of the tools you want to allow or block, as the upstream Model Context Protocol (MCP) server publishes them. Aembit doesn’t match the prefixed names an AI agent displays. See [Tool name matching](reference.md#mcp-tool-name-matching).

## How the MCP tool list works

MCP Tool Access Control makes two decisions about every MCP tool: Tool Visibility for whether an AI agent can discover it, and Tool Invocation for whether an AI agent can call it. The panel sets **Allow** or **Block** for both controls, then **All** or **Specific** for each control.

Each decision works from a list of MCP tools that you create:

* Add some MCP tools to the list and your choices apply to those MCP tools.
* Add no MCP tools to the list and every MCP tool still gets a decision.

An empty MCP tool list saves without warning. It’s valid, but there are specific outcomes.

> **An empty list can invert Allow and Block**
>
> **Specific** acts only on the tools you list, so with an empty list it has nothing to act on:
>
> * **Allow** allows no tools.
> * **Block** blocks no tools.

**Allow** an empty list:

| Tool visibility | What an AI agent discovers | Tool invocation | What an AI agent calls |
| --------------- | -------------------------- | --------------- | ---------------------- |
| All             | All tools                  | All             | All tools              |
| Specific        | No tools                   | All             | Already-known tools    |
| All             | All tools                  | Specific        | No tools               |
| Specific        | No tools                   | Specific        | No tools               |

**Block** an empty list:

| Tool visibility | What an AI agent discovers | Tool invocation | What an AI agent calls |
| --------------- | -------------------------- | --------------- | ---------------------- |
| All             | No tools                   | All             | No tools               |
| Specific        | All tools                  | All             | No tools               |
| All             | No tools                   | Specific        | Already-known tools    |
| Specific        | All tools                  | Specific        | All tools              |

## Add an MCP Tool Access Control Content Security Provider

MCP Tool Access Control has no connection details to enter.

To add the provider to an Access Policy, follow these steps:

1. Log in to your Aembit Tenant, and go to **Access Policies** in the left sidebar.

2. Open the Access Policy for your MCP server, or click **+ New** to create it.

3. In the **Content Security** card, click **+ Configure** to open the Content Security panel.

   ![Content Security card with the Configure action in the Access Policy Builder](https://docs.aembit.io/_astro/apb-content-security-card.CnpOg-Nb_ozQ3a.webp)

4. Leave the **Active** toggle on to enforce Content Security on this Access Policy.

5. Enter a **Name** and optional **Description**.

6. From the **Content Security Type** dropdown, select **Aembit MCP Tool Access Control**.

7. In the **MCP Tool Visibility & Invocation Control** section, set **Allow** or **Block**.

8. Set **Tool Visibility** to **Specific** or **All**.

9. Set **Tool Invocation** to **Specific** or **All**.

   > **Hide and block to fully deny a tool**
   >
   > An AI agent that already knows a tool’s name can still call it, even when Tool Visibility hides it. Block the tool in both controls when you want to be certain an AI agent can’t call it.

10. For each tool you want to govern, click **+ Add Tool**, then enter an exact tool name or a glob pattern in the **MCP Tool Name** field.

    A glob pattern covers a family of tools with one entry, including tools the MCP server adds later. For example, `admin_*` covers `admin_settings` and `admin_reset`.

    Use the name the MCP server publishes, not the prefixed version, or the entry can’t match.

11. (Optional) Edit the **Visibility** or **Invocation** checkbox for tools.

    If you set either to **Specific**, a tool row shows a checkbox.

    A checked box applies the column’s setting to that tool. An unchecked box doesn’t.

12. Click **Save** to add Content Security to the Access Policy.

    ![Content Security panel configured with MCP Tool Access Control in Block mode](https://docs.aembit.io/_astro/apb-content-security-mcp-tool-access-control.D31d7Apx_ZJKPP7.webp)

## Search and remove tools

To search a long list, use the **Search** box.

To remove one tool, click the **X** in its row.

To remove every tool, click **Remove Tools**, then click **Confirm**. Removing every tool at once can’t be undone.

## Terraform

You can manage Content Security with the [Aembit Terraform Provider](https://registry.terraform.io/providers/Aembit/aembit/latest). See the [`aembit_content_security` resource](https://registry.terraform.io/providers/Aembit/aembit/latest/docs/resources/content_security) for the full configuration reference.
