---
type: how-to
title: "Create a AWS S3 Log Stream"
description: "This page describes how to create a new Log Stream to an AWS S3 Bucket"
resource: https://docs.aembit.io/user-guide/administration/log-streams/aws-s3/
interface: web-ui
tags: [log-stream, administration]
timestamp: 2025-05-22T22:55:26-07:00
type_inferred: true
---

# Create a AWS S3 Log Stream

To create a new Log Stream to an AWS S3 Bucket, follow these steps:

1. Log into your Aembit Tenant.

2. Click **Administration** in the left sidebar.

3. At the top, select **Administration ☰ Log Streams**.

   Aembit displays the **Log Streams** page with a list of existing Log Streams.

   ![Log Streams Main Page](https://docs.aembit.io/_astro/log_streams_main_screen.DlYkrO0D_2hdCJC.webp)

4. Click **+ New**, which displays the Log Streams pop out menu.

   ![Log Streams - AWS S3](https://docs.aembit.io/_astro/log_streams_aws_s3_bucket.BQTYTZYe_Z1i10KR.webp)

5. Fill out the following fields:

   * **Name** - The name of the new Log Stream you want to create.

   * **Description** - A text description for the new Log Stream.

   * **Event Type** - Select the type of event you want to stream to your AWS S3 Bucket. Choose from: `Access Authorization Events`, `Audit Logs`, and `Workload Events`

6. Select **AWS S3 using Bucket Policy** as the **Destination Type**.

   For more detailed information on how to create an AWS S3 Bucket, please refer to the [Amazon AWS S3](https://docs.aws.amazon.com/AmazonS3/latest/userguide/creating-bucket.html) technical documentation.

7. Fill out the revealed fields:

   * **S3 Bucket Region** - Enter the AWS region where your S3 bucket is located.

   * **S3 Bucket Name** - Enter the name of your S3 bucket.

   * **S3 Path Prefix** - Enter the path prefix for your S3 bucket.

8. Apply the contents of the **Destination Bucket Policy (Recommended)** field to your destination AWS S3 Bucket.

9. Click **Save**.

   Aembit displays the **Log Stream** on the **Log Streams** page.
