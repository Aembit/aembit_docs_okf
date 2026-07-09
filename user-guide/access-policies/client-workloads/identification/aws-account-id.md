---
type: how-to
title: "AWS Account ID"
description: "How to identify AWS workloads using the AWS Account ID within Aembit"
resource: https://docs.aembit.io/user-guide/access-policies/client-workloads/identification/aws-account-id/
interface: web-ui
tags: [aws, identification, client-workload, access-policy]
timestamp: 2025-06-06T13:01:11-04:00
type_inferred: true
---

# AWS Account ID

This page explains how to use the **AWS Account ID** identifier to uniquely identify workloads deployed on **AWS**.

## Understanding the AWS Account ID identifier

[Section titled “Understanding the AWS Account ID identifier”](#understanding-the-aws-account-id-identifier)

When you deploy applications to AWS, you use the account ID to isolate and group resources by ownership or environment. Each AWS Account owns the resources associated with it. For more info, see [“View AWS account identifiers](https://docs.aws.amazon.com/accounts/latest/reference/manage-acct-identifiers.html) in the AWS docs.

## Applicable deployment type

[Section titled “Applicable deployment type”](#applicable-deployment-type)

Aembit supports the AWS Account ID identification method for Edge-based deployments on [Virtual Machines](../../../deploy-install/virtual-machine/overview.md) deployed to AWS.

## Create a Client Workload with an AWS Account ID identifier

[Section titled “Create a Client Workload with an AWS Account ID identifier”](#create-a-client-workload-with-an-aws-account-id-identifier)

To configure a Client Workload with an AWS Account ID identifier, follow these steps:

1. Log into your Aembit Tenant.

2. Click **Client Workloads** in the left nav pane.

3. Click **New**, revealing the **Client Workload** pop out menu.

4. Enter the **Name** and optional **Description** for the Client Workload.

5. Under **Client Identification**, select **AWS Account ID**.

   For **Value**, enter the 12-digit AWS Account ID *without spaces and dashes* where the workload is running.

   For example, if your AWS account ID is `1234-5678-9012`, then enter `123456789012` in the **Value** field.

   If you don’t know the AWS Account ID or how to find it, see [Find AWS Account ID](#find-aws-account-id).

6. Click **Save**.

   Aembit displays the new Client Workload on the **Client Workloads** page.

Client Workload identifier uniqueness

When you identify a Client Workload using a single identifier such as this one in a complex environment, this identifier may not always provide sufficient uniqueness.

To avoid Aembit unintentionally matching something beyond what it’s intended to match, Aembit recommends that you set additional identifiers to compliment this one, creating a Client Workload identity that’s unique across your environment.

See [Using multiple Client Workload identifiers](client-workload-multiple-ids.md) for guidance on combining this identifier with more specific identifiers such as those on the [Client Workload Identifiers overview](overview.md) page, which includes vendor- and technology-specific identifiers.

## Find AWS account id

[Section titled “Find AWS account id”](#find-aws-account-id)

To find your AWS Account ID in the AWS Console, follow these steps:

Note

You can also find the AWS Account ID in billing settings, IAM dashboard, or by using the AWS CLI.

Be sure to enter the full 12-digit ID *without spaces and dashes*.

1. Open the [AWS Management Console](https://console.aws.amazon.com/).

2. Click the Account Menu that displays your AWS username in the top-right corner.

3. Click the **Copy Account ID** icon next to your 12-digit AWS Account ID.

   Use this value in your Aembit configuration, *remembering to enter it without spaces or dashes*.

## Related

**Compatible trust providers**

* [AWS Metadata Service](../../trust-providers/aws-metadata-service-trust-provider.md)
* [AWS Role](../../trust-providers/aws-role-trust-provider.md)
