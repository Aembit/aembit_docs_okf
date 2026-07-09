---
type: how-to
title: "Create a Google Cloud Storage Bucket Log Stream"
description: "This page describes how to create a new Log Stream to an Google Cloud Storage (GCS) Bucket"
resource: https://docs.aembit.io/user-guide/administration/log-streams/gcs-bucket/
interface: web-ui
tags: [log-stream, administration]
timestamp: 2025-05-22T22:55:26-07:00
type_inferred: true
---

# Create a Google Cloud Storage Bucket Log Stream

## Prerequisites

[Section titled “Prerequisites”](#prerequisites)

Before creating a new Google Cloud Storage (GCS) Bucket Log Stream, make sure you have set up and configured:

* [Google Cloud Storage Bucket](https://cloud.google.com/storage/docs/creating-buckets)

* [IAM Service Account](https://cloud.google.com/iam/docs/service-accounts-create)

* [Workload Identity Federation](https://cloud.google.com/iam/docs/workload-identity-federation-with-other-providers)

## Create a new Google Cloud Storage Bucket Log Stream

[Section titled “Create a new Google Cloud Storage Bucket Log Stream”](#create-a-new-google-cloud-storage-bucket-log-stream)

To create a new Log Stream for a Google Cloud Storage (GCS) Bucket, follow these steps:

1. Log into your Aembit Tenant.

2. Click **Administration** in the left sidebar.

3. At the top, select **Administration ☰ Log Streams**.

   Aembit displays the **Log Streams** page with a list of existing Log Streams.

   ![Log Streams Main Page](https://docs.aembit.io/_astro/log_streams_main_screen.DlYkrO0D_2hdCJC.webp)

4. Click **+ New**, which displays the Log Streams pop out menu.

   ![Log Streams Dialog Window - Empty](https://docs.aembit.io/_astro/gcs_log_streams_dialog_window_empty.BNYAV5la_Nx0aH.webp)

5. Fill out the following fields:

   * **Name** - The name of the new Log Stream you want to create.
   * **Description** - A text description for the new Log Stream.
   * **Event Type** - Select the type of event you want to stream to your GCS Bucket. Choose from: `Access Authorization Events`, `Audit Logs`, and `Workload Events`

6. Select **GCS Bucket using Workload Identity Federation** as the **Destination Type**.

7. Fill out the revealed fields:

8. Add your information for the Google Cloud Storage Bucket in the following fields:

   * **Bucket Name** - Name of the bucket.

   * **Audience** - The value from the **Provider Details** in your GCS Bucket Console.

     Aembit matches any audience value you specific for the provider, and can be either the default audience or a custom value.

   * **Service Account Email** - The email address of the Service Account (set at the time of Service Account creation).

   * **Token Lifetime** - The amount of time that the token will remain active.

9. Click **Save**.

   Aembit displays the **Log Stream** on the **Log Streams** page.

   ![Log Streams Main Page With GCS Bucket Log Stream Added](https://docs.aembit.io/_astro/gcs_log_streams_log_stream_list_with_gcs_bucket.Dw0RLiFK_Z1wir4p.webp)
