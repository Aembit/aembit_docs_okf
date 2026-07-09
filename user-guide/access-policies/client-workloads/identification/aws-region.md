---
type: how-to
title: "AWS Region"
description: "How to identify AWS workloads using the AWS Region within Aembit"
resource: https://docs.aembit.io/user-guide/access-policies/client-workloads/identification/aws-region/
interface: web-ui
tags: [aws, identification, client-workload, access-policy]
timestamp: 2025-06-06T13:01:11-04:00
type_inferred: true
---

# AWS Region

This page explains how to use the **AWS Region** identifier to uniquely identify workloads deployed on **AWS**.

## Understanding the AWS Region identifier

[Section titled “Understanding the AWS Region identifier”](#understanding-the-aws-region-identifier)

When you deploy applications to AWS, you use the region to isolate and group resources by geographic location. Each AWS Region contains multiple availability zones and is useful for controlling latency and data residency. For more info, see [“Regions and Availability Zones”](https://aws.amazon.com/about-aws/global-infrastructure/regions_az/) in the AWS docs.

## Applicable deployment type

[Section titled “Applicable deployment type”](#applicable-deployment-type)

Aembit supports the AWS Region identification method for Edge-based deployments on [Virtual Machines](../../../deploy-install/virtual-machine/overview.md) deployed to AWS.

## Create a Client Workload with an AWS Region identifier

[Section titled “Create a Client Workload with an AWS Region identifier”](#create-a-client-workload-with-an-aws-region-identifier)

To configure a Client Workload with an AWS Region identifier, follow these steps:

1. Log into your Aembit Tenant.

2. Click **Client Workloads** in the left nav pane.

3. Click **New**, revealing the **Client Workload** pop out menu.

4. Enter the **Name** and optional **Description** for the Client Workload.

5. Under **Client Identification**, select **AWS Region**.

   For **Value**, enter the AWS Region where the workload is running.

   For example, if your AWS Region is `us-west-2`, enter that in the **Value** field.

   If you don’t know the AWS Region or how to find it, see [Find AWS Region](#find-aws-region).

6. Click **Save**.

   Aembit displays the new Client Workload on the **Client Workloads** page.

Client Workload identifier uniqueness

When you identify a Client Workload using a single identifier such as this one in a complex environment, this identifier may not always provide sufficient uniqueness.

To avoid Aembit unintentionally matching something beyond what it’s intended to match, Aembit recommends that you set additional identifiers to compliment this one, creating a Client Workload identity that’s unique across your environment.

See [Using multiple Client Workload identifiers](client-workload-multiple-ids.md) for guidance on combining this identifier with more specific identifiers such as those on the [Client Workload Identifiers overview](overview.md) page, which includes vendor- and technology-specific identifiers.

## Find AWS region

[Section titled “Find AWS region”](#find-aws-region)

To find your AWS Region in the AWS Console, follow these steps:

Note

You can also find the AWS Region in the resource metadata, EC2 dashboard, or by using the AWS CLI.

Be sure to enter the region in its standard format (for example `us-east-1`, `eu-central-1`).

1. Go to the [AWS Management Console](https://console.aws.amazon.com/).

2. Open the service (for example, EC2) that hosts your resource.

3. You’ll see the region in the top-right corner of the Console or in the resource’s details.

   Use this value in your Aembit configuration.

## Related

**Compatible trust providers**

* [AWS Metadata Service](../../trust-providers/aws-metadata-service-trust-provider.md)
* [AWS Role](../../trust-providers/aws-role-trust-provider.md)
