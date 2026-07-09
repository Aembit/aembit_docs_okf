---
type: explanation
title: "Discovery overview"
resource: https://docs.aembit.io/user-guide/administration/discovery/
tags: [discovery, administration]
timestamp: 2025-06-03T14:04:16-07:00
type_inferred: true
---

# Discovery overview

**Discovery** serves as the central control board for managing integrations related to the [Discovery](../../discovery/overview.md) process.

Note

This is a beta feature and may be subject to changes. To enable Discovery with Wiz, contact Aembit by completing the [Contact Us form](https://aembit.io/contact/).

Once you’ve contacted Aembit to enable Discovery in your Aembit Tenant, you can configure an integration to find workloads in your environment. Once you configure an integration, Aembit uses it to discover workloads. After discovering your workloads, Aembit displays them in either the **Client Workload** or **Server Workload** tab as **Discovered**. For detailed instructions on managing discovered workloads, refer to [Interacting with Discovered Workloads](../../discovery/managing-discovered-workloads.md).

## Using the discovery tab

[Section titled “Using the discovery tab”](#using-the-discovery-tab)

On the **Discovery tab** page, the **New** option appears in the top-right corner. Clicking **New** allows you to create and configure new integrations.

![Discovery Tab Layout](https://docs.aembit.io/_astro/administration_discovery_main_page.CEIVH_vO_1C18La.webp)

Following that, Aembit displays the **Integrations** list which lists existing integrations in a table. Each row in the table shows key details such as:

* **Name** - The name of the integration.
* **Type** - The type of integration.
* **Last Successful Sync** - The date and time of the last successful synchronization.
* **Sync Status** - Indicates the synchronization status.

To interact with an integration, you can either:

* Hover over the row in the **Integrations List**, where a three-dotted icon appear on the right end of the row. Clicking this icon opens a menu where you can:

  * **View details** - See more information about the integration.
  * **Edit** - Modify the integration’s configuration.
  * **Delete** - Remove the integration.
  * **Change active status** - Activate or deactivate the integration.

* Or, you can click directly on the integration row, which opens a **details page** where they can view, edit, or delete the integration.

Additionally, you can hover over the **Name** column to see the **ID** of the integration, which they can copy for reference.

## Related resources

[Section titled “Related resources”](#related-resources)

For more information about Discovery, see the following related pages:

* [Discovery Overview](../../discovery/overview.md) - Learn about the Discovery feature in Aembit
* [Managing Discovered Workloads](../../discovery/managing-discovered-workloads.md) - Learn how to work with discovered workloads
* [Discovery Sources](../../discovery/sources/overview.md) - Learn about the different Discovery Sources available in Aembit
