---
type: reference
title: "CrowdStrike AIDR Content Security"
description: "How CrowdStrike AIDR inspects MCP content as a Content Security Provider, and where to get credentials."
resource: https://docs.aembit.io/user-guide/access-policies/content-security/crowdstrike-aidr/
interface: web-ui
tags: ["crowdstrike-aidr", "content-security", "access-policy"]
timestamp: 2026-09-09T08:20:13-07:00
---

# CrowdStrike AIDR Content Security

CrowdStrike AI Detection and Response (AIDR) is a Content Security Provider that inspects the Model Context Protocol (MCP) traffic flowing through Aembit’s MCP Identity Gateway. AIDR returns an allow, block, or transform verdict for the content it inspects, and Aembit enforces that verdict.

> **Note**
>
> CrowdStrike AIDR requires an active CrowdStrike AIDR for Agents subscription. To obtain access, contact CrowdStrike.

To add CrowdStrike AIDR to a policy, see [Add CrowdStrike AIDR to a policy](add-to-policy.md).

## How CrowdStrike AIDR inspects MCP traffic

CrowdStrike AIDR inspects content at three checkpoints on the MCP request path:

* **Tool listings** - The set of tools an MCP server advertises to a client.
* **Tool call inputs** - The arguments a client sends when it calls a tool.
* **Tool call outputs** - The results a tool returns to the client.

At each checkpoint, AIDR evaluates the content in real time and returns one of three verdicts:

* **Allow** - The content passes inspection, and Aembit forwards it unchanged.
* **Block** - The content violates a CrowdStrike AIDR policy, and Aembit blocks it.
* **Transform** - AIDR returns modified content, and Aembit forwards the transformed content in place of the original.

Aembit enforces these verdicts across all three checkpoints. CrowdStrike AIDR has no per-checkpoint toggles to configure.

> **Inspection is per MCP message**
>
> Content Security evaluates each MCP message on its own. A verdict covers only the single tool listing, tool call input, or tool call output the inspection service evaluated, not the conversation, session, or task that message belongs to. Blocking one message doesn’t block the messages that follow it. If an AI agent retries a blocked call with different arguments and the new message passes inspection, Aembit forwards it.

> **Note**
>
> “Report,” or report-only, is an enforcement mode configured on the CrowdStrike side and isn’t an Aembit action. Aembit applies allow, block, and transform.

## Fail-open and fail-closed behavior

The **Fail Open on Error** setting controls what Aembit does when it can’t reach CrowdStrike AIDR or an inspection request errors:

* **On (fail open, default)** - Aembit forwards the request without inspection. MCP traffic keeps flowing during an AIDR outage, but Aembit doesn’t inspect that traffic while AIDR is unavailable.
* **Off (fail closed)** - Aembit blocks the request. No MCP traffic bypasses inspection, but an AIDR outage interrupts MCP traffic until AIDR recovers.

The setting is a trade-off between availability and guaranteed inspection. You set **Fail Open on Error** when you add CrowdStrike AIDR to a policy.

An inspection error records an error in the workload event under either setting. When Aembit fails open, it forwards the content and still records the error, so an error in the event doesn’t by itself mean Aembit blocked the content.

### AIDR content size limit

CrowdStrike AIDR rejects inspection requests whose content exceeds its size limit and returns an HTTP 413 (Content Too Large) response. CrowdStrike sets this limit, and there’s no corresponding setting in Aembit. Aembit treats that response like any other inspection error, so **Fail Open on Error** determines whether the content passes without inspection or Aembit blocks it. Aembit records the status AIDR returned in the workload event, so a 413 in the event’s error message identifies oversized content as the cause.

**Max Retries** doesn’t help in this case. Each retry sends the same oversized content and receives the same response, so retries only add latency.

Large tool call outputs are the most common cause. Some MCP tools accept a parameter that bounds the size of their results, such as a page size or a maximum result count. Setting that parameter keeps tool outputs small enough for AIDR to inspect.

## Get your credentials

You configure the integration with two values from CrowdStrike AIDR: the AIDR base URL and an API token. To get them:

1. In CrowdStrike AIDR, go to **Collectors** and select **+ Collector**.

2. Select **Agentic**, select the **MCP** collector, then select **Next**.

3. Enter a **Collector Name**, select a **Logging** option, select the **Policy** you want to enforce, then select **Save**.

4. On the collector’s **Config** tab, copy the **API Base URL** and **API Token** values from **API Token Details**.

CrowdStrike and Aembit use different names for these two values. The API Base URL is the **AIDR Base URL** in Aembit, and the API Token is the **AIDR Token**. Copy both values from the collector rather than typing them. The base URL includes your CrowdStrike cloud region, so it differs between tenants.

The collector’s **Config** tab also shows the token’s expiration date. CrowdStrike deletes a rotated token after a seven-day grace period, which is the window you have to update the value in Aembit.

## CrowdStrike AIDR versus the CrowdStrike Access Condition

Aembit integrates with two different CrowdStrike products through two different Access Policy components:

* **CrowdStrike AIDR** is a Content Security Provider that inspects MCP content. You add it as the Content Security Provider on an Access Policy.
* **CrowdStrike** endpoint posture is an [Access Condition](../../access-conditions/crowdstrike.md) integration that evaluates the reported state of a Client Workload‘s endpoint. You configure it on an Access Condition.

These integrations are separate and serve different purposes. Configuring the CrowdStrike Access Condition doesn’t enable CrowdStrike AIDR Content Security, and configuring CrowdStrike AIDR doesn’t enable the CrowdStrike Access Condition.

## Data shared for inspection

When a policy includes CrowdStrike AIDR, Aembit sends the inspected MCP content to CrowdStrike AIDR. This content includes the MCP tool listings, MCP tool call inputs, and MCP tool call outputs on the request path. Aembit also sends request metadata that identifies the request, such as the user and agent identifiers and the application and server names involved. Review CrowdStrike’s data handling before you add CrowdStrike AIDR to policies that carry sensitive content.
