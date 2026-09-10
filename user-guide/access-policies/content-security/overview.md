---
type: reference
title: "Content Security"
description: "Overview of Content Security Providers and how they govern MCP tool traffic in an Access Policy."
resource: https://docs.aembit.io/user-guide/access-policies/content-security/
interface: web-ui
tags: ["content-security", "access-policy"]
timestamp: 2026-09-09T08:20:13-07:00
---

# Content Security

Content Security Providers govern the Model Context Protocol (MCP) tool traffic that flows through Aembit’s MCP Identity Gateway to your MCP servers. You add a provider to an Access Policy through its Content Security component. Each provider evaluates the MCP tool messages the MCP Identity Gateway proxies, and Aembit applies the provider’s decision before the message continues.

## Content Security in an Access Policy

In the Access Policy Builder, Content Security is an optional component positioned between Access Conditions and Credential Providers. See [Create an Access Policy](../create-access-policy.md) for every component a policy can include and whether each one is required.

A policy evaluates its Content Security Provider on each matching request, adding an evaluation step to the request path without changing how the policy’s other components authorize access.

> **Reuse this in another Resource Set**
>
> You can copy a Content Security Provider to another Resource Set. See [About component copying](../../administration/resource-sets/about-component-copying.md) to learn more.

## Available providers

* [CrowdStrike AIDR](crowdstrike-aidr/overview.md) - Inspect MCP content with CrowdStrike AI Detection and Response (AIDR).

## Data shared for inspection

When a policy includes CrowdStrike AIDR, Aembit sends the inspected MCP content to CrowdStrike AIDR, along with request metadata that identifies the request. Review CrowdStrike’s data handling before you add CrowdStrike AIDR to policies that carry sensitive content. For exactly what Aembit sends, see [Data shared for inspection](crowdstrike-aidr/overview.md#data-shared-for-inspection).

## Where Aembit records a provider’s decisions

Aembit records every decision a Content Security Provider makes, so you can confirm what a provider decided:

* [MCP workload events](../../audit-report/workload-events/supported-protocols.md#content-security) record the decision for each MCP message under `application.mcp.contentSecurity`.
* [Access Authorization Events](../../audit-report/access-authorization-events.md#content-security) name the provider that Aembit identified for the policy.
