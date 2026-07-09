---
type: how-to
title: "AWS EC2 Instance ID"
description: "How to identify AWS workloads using the AWS EC2 Instance ID within Aembit"
resource: https://docs.aembit.io/user-guide/access-policies/client-workloads/identification/aws-ec2-instance-id/
interface: web-ui
tags: [aws, identification, client-workload, access-policy]
timestamp: 2025-06-06T13:01:11-04:00
type_inferred: true
---

# AWS EC2 Instance ID

This page explains how to use the **AWS EC2 Instance ID** identifier to uniquely identify workloads deployed on **AWS**.

## Understanding the AWS EC2 instance ID identifier

[Section titled “Understanding the AWS EC2 instance ID identifier”](#understanding-the-aws-ec2-instance-id-identifier)

When you deploy applications to AWS, you often refer to specific virtual machine instances using their EC2 Instance IDs. AWS assigns a unique identifier to each EC2 instance when it launches. For more info, see [“What is Amazon EC2?”](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Using_InstanceStraightToIdentifiers.html) in the AWS docs.

## Applicable deployment type

[Section titled “Applicable deployment type”](#applicable-deployment-type)

Aembit supports the AWS EC2 Instance ID identification method for Edge-based deployments on [Virtual Machines](../../../deploy-install/virtual-machine/overview.md) deployed to AWS.

## Create a Client Workload with an AWS EC2 Instance ID identifier

[Section titled “Create a Client Workload with an AWS EC2 Instance ID identifier”](#create-a-client-workload-with-an-aws-ec2-instance-id-identifier)

To configure a Client Workload with an AWS EC2 Instance ID identifier, follow these steps:

1. Log into your Aembit Tenant.

2. Click **Client Workloads** in the left nav pane.

3. Click **New**, revealing the **Client Workload** pop out menu.

4. Enter the **Name** and optional **Description** for the Client Workload.

5. Under **Client Identification**, select **AWS EC2 Instance ID**.

   For **Value**, enter the EC2 Instance ID where the workload is running.

   For example, if your EC2 Instance ID is `i-0123456789abcdef0`, enter that in the **Value** field.

   If you don’t know the EC2 Instance ID or how to find it, see [Find EC2 Instance ID](#find-ec2-instance-id).

6. Click **Save**.

   Aembit displays the new Client Workload on the **Client Workloads** page.

## Find EC2 instance ID

[Section titled “Find EC2 instance ID”](#find-ec2-instance-id)

To find your EC2 Instance ID in the AWS Console, follow these steps:

Note

You can also find the EC2 Instance ID using the EC2 dashboard or the AWS CLI.

Be sure to enter the full instance ID exactly as displayed by AWS.

1. **Open the AWS Console**

   Go to the [AWS Management Console](https://console.aws.amazon.com/).

2. **Navigate to the EC2 Dashboard**

   From the Services menu, choose **EC2**, then click **Instances**.

3. **Locate the Instance ID**

   You can find the EC2 Instance ID in the **Instance ID** column for each running instance.

   Use this value in your Aembit configuration.

## Related

**Compatible trust providers**

* [AWS Metadata Service](../../trust-providers/aws-metadata-service-trust-provider.md)
* [AWS Role](../../trust-providers/aws-role-trust-provider.md)
