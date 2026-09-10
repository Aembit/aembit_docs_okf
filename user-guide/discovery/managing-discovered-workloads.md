---
type: how-to
title: "Managing discovered workloads"
description: "How to manage workloads found through Aembit Discovery"
resource: https://docs.aembit.io/user-guide/discovery/managing-discovered-workloads/
interface: web-ui
tags: ["discovery"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Managing discovered workloads

> **Note**
>
> This is a beta feature and may be subject to changes.

This section explains how to manage discovered workloads—view their details, convert them to managed, ignore them, and restore them if needed.

Once Aembit has completed the discovery process, you can find your discovered workloads in the **Discovered tab** on either the Client Workloads or Server Workloads left nav menu options. Aembit displays the following:

![Discovered Client Workloads page](https://docs.aembit.io/_astro/discovery-client-workloads.ByRAoMa__Z7MR88.webp)

![Discovered Server Workloads page](https://docs.aembit.io/_astro/discovery-server-workloads.CO6lWVls_ejY2X.webp)

Use the dropdown in the top-right corner to filter workloads by state:

* **Discovered** - Workloads Aembit has found but aren’t yet managed.
* **Ignored** - Workloads marked as irrelevant, which no longer appear in the main list.

Following that, Aembit displays a table of all discovered or ignored workloads. The table includes the following columns for Client and Server Workloads:

* **Name** - The name of the workload in your Aembit Tenant. For Server Workloads, this defaults to the hostname of the workload, but you can change it to a more descriptive name.
* **Platform** - The platform the workload is running on.
* **Account** - The account the workload uses on the platform.
* **Region** - The platform’s region the workload is running in.
* **Workload Type** - The type of workload.
* **Host/Port/Protocol** - (Server Workloads only) The service endpoint details for Server Workloads.
* **Source** - Indicates where Aembit discovered the workload.
* **Client Workload Identifiers** - (Client Workloads only) The identification type for Client Workloads. For the full list, see [Client Workload identifiers overview](../access-policies/client-workloads/identification/overview.md).
* **Activity** - Displays connections to the workload over a period of time. This helps you understand how often clients access and use the workload.

## Filtering Discovered Workloads

You can filter the discovered workloads based on different criteria to find the workloads you need. As you filter, Aembit updates the list of discovered workloads to match your criteria. This enables you to narrow your search and locate specific workloads without having to scroll through the entire list, especially if you have many discovered workloads.

![Discovered Client Workloads filtered](https://docs.aembit.io/_astro/discovery-client-workload-filters-chosen.B8xBAhRa_sJ7uQ.webp)

![Discovered Server Workloads filtered](https://docs.aembit.io/_astro/discovery-server-workload-filters-chosen.DxHmsmJ1_WXWvs.webp)

The following sections detail the filtering options available for Client and Server Workloads:

### Client Workload filtering options

On the **Client Workloads** page in the **Discovered** tab, you can filter for specific workloads based on the following:

* **SOURCE** - Filter by [Workload Discovery Source](sources/overview.md)
* **PLATFORM** - Filter by the platform the Client Workload is running on (for example, AWS, Azure, and GCP).
* **ACCOUNT** - Filter by the account the Client Workload uses on the platform (for example, AWS Account ID, Azure Subscription ID, or GCP Project ID).
* **REGION** - Filter by the platform’s region the Client Workload is running in.
* **WORKLOAD TYPE** - Filter by the type of Client Workload (for example, AWS Lambda, Azure Bucket, and more).
* **IDENTIFIERS** - Filter by [Client Workload Identifier](../access-policies/client-workloads/identification/overview.md) (for examples, AWS Account ID, Azure Bucket, GCP hostname, and many more).

![Client Workload Discovered tab filtering options](https://docs.aembit.io/_astro/discovery-filtering-client-workloads.BH5W0ek7_1wUpY6.webp)

### Server Workload filtering options

On the **Server Workloads** page in the **Discovered** tab, you can filter for specific workloads based on the following:

* **SOURCE** - Filter by [Workload Discovery Source](sources/overview.md).
* **PLATFORM** - Filter by the platform the Server Workload is running on (for example, AWS, Azure, and GCP).
* **ACCOUNT** - Filter by the account the Server Workload uses on the platform (for example, AWS Account ID, Azure Subscription ID, or GCP Project ID).
* **REGION** - Filter by the platform’s region the Server Workload is running in.
* **WORKLOAD TYPE** - Filter by the type of Server Workload (for example, AWS EC2, Azure VM, and more).
* **PROTOCOL** - Filter by the protocol the Server Workload is using
* **PORT** - Filter by the port the Server Workload is using

![Server Workload Discovered tab filtering options](https://docs.aembit.io/_astro/discovery-filtering-server-workloads.DYgQ_RTL_Z7jji8.webp)

## Viewing workload details

On the **Discovered** tab, you can view the details of each workload that Aembit has discovered. However, you can’t edit the details of discovered workloads directly from this page. Instead, you must first convert them to **managed** workloads to edit their details or you can ignore them if they’re not relevant to your use case.

On a workload’s detail page, you can choose to manage or ignore the workload at the top of the form using **+ Manage** or **Ignore** respectively.

On the left side of the page, Aembit displays the workload’s details. Aembit auto-populates these fields with the information it fetches from the Discovery Source. On the right side, Aembit displays the associated metadata for the workload.

The details on this page differ between Client and Server Workloads:

![Discovered Client Workload details page](https://docs.aembit.io/_astro/discovered-client-workload-details.BDvtlrEy_1CE5Ok.webp)

![Discovered Server Workload details page](https://docs.aembit.io/_astro/discovered-server-workload-details._lYmw4MT_lrttb.webp)

**Client Workloads** display the following details:

* **Name** - The name of the workload in your Aembit Tenant.
* **Client Identification** - The [Client Workload identifiers](../access-policies/client-workloads/identification/overview.md) types associated with this Client Workload. For the full list, see .

**Server Workloads** display the following details:

* **Name** - The name of the workload in your Aembit Tenant.

* **Service Endpoint** - The service endpoint details for Server Workloads, including:

  * **Host** - The hostname or IP address of the Server Workload.
  * **Port** - The port number the Server Workload is using.
  * **Protocol** - The protocol the Server Workload is using (for example, HTTP, HTTPS, TCP, etc.).

* **Authentication** - The authentication type for the Server Workload.

### View workload details

To view the details of a discovered workload, follow these steps:

1. In left nav menu, click either **Client Workloads** or **Server Workloads**.

2. Select the **Discovered tab**.

   This displays all discovered workloads in a table format.

3. Click any row in the **Discovered** list to go to the details page for that specific workload.

4. (Optional) If you need more detailed data, click the **View JSON** to access the full JSON data associated with the workload.

   This allows you to inspect all the metadata and relevant details for the workload in its raw format.

   Expand to see an example

   ![Raw JSON output of workload details](https://docs.aembit.io/_astro/discovered-workload-details-raw.DhhCgnnZ_1DBBs7.webp)

## Manage a discovered workload

After [reviewing a workload’s details](#view-workload-details) and deciding to manage it, follow these steps to convert that workload to **managed**:

1. On the workload you want to convert, click **+ Manage**.

   This opens the workload in **edit mode**, allowing you to make any necessary changes to its configuration or settings.

2. Once you’re satisfied with the details, click **Save** to complete the management process.

   Once saved, the workload moves from the **Discovered tab** to the **Managed tab**, where you can use it in Access Policies.

You can then return to the **Managed tab** to create and apply Access Policies for the workload.

> **Mmanaging multiple workloads**
>
> When selecting more than one workload from the **Discovered** tab, you only have the option to **Ignore**. You can only manage one workload at a time from this tab.
>
> ![Comparison of single vs multiple selected workloads](https://docs.aembit.io/_astro/discovery-client-workload-multi-item-selection-comparison.DmcOjNtX_Z1jCRYg.webp)

## Ignore a discovered workload

If you find a workload unnecessary or irrelevant, and you no longer want to see it in the **Discovered tab**, do the following:

1. Go to the **Discovered tab** in either the Client Workloads or Server Workloads left nav menu.

2. Select the workloads you want to ignore in the **Discovered** list by checking the checkbox next to each workload.

   Alternatively, you can go to a workload’s details page and click **Ignore**.

3. Click **Ignore**.

   Aembit moves the workload to the **Ignored** list, removing it from the **Discovered** list. This helps keep your Discovered list focused on relevant workloads.

![Ignoring discovered Client Workloads](https://docs.aembit.io/_astro/discovery-client-workload-select-to-ignore.Bx0QBpK5_ZFV4yw.webp)

![Ignoring discovered Server Workloads](https://docs.aembit.io/_astro/discovery-server-workload-select-to-ignore.C5S0_hc8_1mN4WU.webp)

You can always [restore an ignored workload](#restore-or-manage-an-ignored-workload) if you change your mind or need to manage it later.

## Restore or manage an ignored workload

To restore workloads to the **Discovered tab**, follow these steps:

1. Go to the **Discovered tab** in either the Client Workloads or Server Workloads left nav menu.

2. Switch the dropdown in the top-right corner to **Ignored**.

   This displays all ignored workloads in a table format.

3. Select the workload you want to restore or manage in the **Ignored** list.

4. At the top-right side of the page, you can either:

   * Click **Restore**.\
     Aembit moves the workload back to the **Discovered tab**, making it eligible for management again.

   * Click **+ Manage**.\
     Aembit opens the workload in **edit mode**, allowing you to make any necessary changes to its configuration or settings before saving it.

   Alternatively, you can go to a workload’s details page and click **Restore** or **+ Manage** respectively.

![Ignoring discovered Client Workloads](https://docs.aembit.io/_astro/discovery-client-workload-ignored-one-item-selection.DDTp7aeD_Z12BopQ.webp)

![Ignoring discovered Server Workloads](https://docs.aembit.io/_astro/discovery-server-workload-ignored-one-item-selection.CUwIXTmC_Z2ge7Qf.webp)

> **Restoring or managing multiple ignored workloads**
>
> When selecting more than one workload from the **Ignored** tab, you only have the option to **Restore**. You can only manage one workload at a time from this tab.
>
> ![Comparison of single vs multiple selected workloads](https://docs.aembit.io/_astro/discovery-client-workload-multi-item-selection-comparison.DmcOjNtX_Z1jCRYg.webp)
