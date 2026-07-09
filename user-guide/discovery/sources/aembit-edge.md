---
type: how-to
title: "Aembit Edge Discovery Source"
description: "How Aembit discovers workloads using the Aembit Edge Discovery Source"
resource: https://docs.aembit.io/user-guide/discovery/sources/aembit-edge/
interface: web-ui
tags: [source, discovery]
timestamp: 2025-06-17T17:22:04-07:00
type_inferred: true
---

# Aembit Edge Discovery Source

Note

This is a beta feature and may be subject to changes.

This page explains how Aembit Edge discovers workloads. Aembit Edge enables efficient workload discovery within your environments, helping you maintain visibility and manage access across your infrastructure.

**Edge Discovery** identifies workloads in [environments](../../../reference/edge-components/edge-component-supported-versions.md) where you’ve deployed **Aembit Edge**. By collecting communication event data, Aembit Edge helps identify workloads and categorize them as either **Managed** or **Discovered** based on predefined criteria.

To perform **Edge Discovery**, you need to deploy **Aembit Edge** to your desired environments. **Aembit Edge** automatically collects event data about workload communication. This data allows Aembit to categorize workloads as either **Managed** or **Discovered** based on predefined criteria.

The process makes sure that Aembit tracks and manages workloads meeting these criteria, while Aembit marks others as **Discovered** for further review.

Aembit Edge helps simplify the management of workloads by automatically identifying which workloads are active and how they’re interacting, providing a comprehensive view of your infrastructure.

### How to perform Edge Discovery

[Section titled “How to perform Edge Discovery”](#how-to-perform-edge-discovery)

1. **Deploy Aembit Edge** to your environment.

   * Ensure you set up your environment to support Aembit Edge. This involves configuring the necessary infrastructure and permissions for the Edge Components.

2. **Ensure your environment generates event data.**

   * Aembit Edge relies on event data from your environment to detect workloads and monitor their interactions. Make sure your environment is actively generating the necessary data for discovery.

3. **Wait for the system to collect the data and categorize the workloads.**

   * Aembit Edge automatically start collecting the event data and categorize workloads as either **Managed** or **Discovered**, depending on whether they meet predefined criteria.

4. **Log out and log back into the Aembit Tenant to trigger the discovery process and refresh the workload data.**

   * Logging out and back in make sure that the system updates with the most recent data and categorization of workloads.

Once discovery is complete, you can view the workloads that Aembit discovered and categorized as **discovered** in the **Client Workloads** or **Server Workloads** sections.

After completing these steps, you’ll have improved visibility into the workloads operating in your environment. To interact with or manage the discovered workloads, visit [Interacting with Discovered Workloads](../managing-discovered-workloads.md) for more details.
