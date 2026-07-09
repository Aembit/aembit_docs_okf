---
type: how-to
title: "AWS ECS Service Name"
description: "How to identify AWS ECS Fargate workloads using the ECS Service name within Aembit"
resource: https://docs.aembit.io/user-guide/access-policies/client-workloads/identification/aws-ecs-service-name/
interface: web-ui
tags: [aws, identification, client-workload, access-policy]
timestamp: 2025-06-16T06:51:42-07:00
type_inferred: true
---

# AWS ECS Service Name

This page explains how to use the **AWS ECS Service Name** to uniquely identify workloads deployed on **AWS ECS Fargate**. The service name is a key identifier for managing ECS workloads at the service level.

## Understanding the AWS ECS service name

[Section titled “Understanding the AWS ECS service name”](#understanding-the-aws-ecs-service-name)

When deploying applications to AWS ECS Fargate, the ECS Service Name provides a stable and descriptive identifier for running services. It represents a long-lived service managed by ECS and helps distinguish different applications or deployment environments. Refer to the [official AWS documentation](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/what-is-amazon-ecs.html) for more information.

## Applicable deployment type

[Section titled “Applicable deployment type”](#applicable-deployment-type)

Aembit specifically supports ECS Service Name identification for Edge-based deployments on [AWS ECS Fargate](../../../deploy-install/serverless/aws-ecs-fargate.md).

## Create a Client Workload with an AWS ECS service name

[Section titled “Create a Client Workload with an AWS ECS service name”](#create-a-client-workload-with-an-aws-ecs-service-name)

To configure a Client Workload using an ECS Service Name, follow these steps:

1. Log into your Aembit Tenant.

2. Click **Client Workloads** in the left nav pane.

3. Click **New**, revealing the **Client Workload** pop out menu.

4. Enter the **Name** and optional **Description** for the Client Workload.

5. Under **Client Identification**, select **AWS ECS Service Name**.

   For **Value**, enter the name of the ECS service you’ve configured in AWS.

   For example, if your service name is `prod-app-service`, enter `prod-app-service` in the **Value** field.

   If you don’t know your ECS Service Name or how to find it, see [Find ECS Service Name in AWS](#find-ecs-service-name-in-aws).

6. Click **Save**.

   Aembit displays the new Client Workload on the **Client Workloads** page.

Client Workload identifier uniqueness

When you identify a Client Workload using a single identifier such as this one in a complex environment, this identifier may not always provide sufficient uniqueness.

To avoid Aembit unintentionally matching something beyond what it’s intended to match, Aembit recommends that you set additional identifiers to compliment this one, creating a Client Workload identity that’s unique across your environment.

See [Using multiple Client Workload identifiers](client-workload-multiple-ids.md) for guidance on combining this identifier with more specific identifiers such as those on the [Client Workload Identifiers overview](overview.md) page, which includes vendor- and technology-specific identifiers.

## Find ECS service name in AWS

[Section titled “Find ECS service name in AWS”](#find-ecs-service-name-in-aws)

To find the ECS Service Name in the AWS Console, follow these steps:

Note

Each ECS Service Name must be unique within the ECS Cluster.

This is the name you’ll use when configuring your Client Workload in the Aembit Tenant.

1. **Open your AWS ECS Console**

   Open the AWS Management Console and go to the Elastic Container Service (ECS).

2. **Select your Cluster**

   In the ECS console, click **Clusters** and select the relevant ECS cluster.

3. **View Services**

   In the selected cluster, go to the **Services** tab.

4. **Locate the Service Name**

   The **Service Name** column under the Services tab lists the ECS Service Names.

   This is the string you’ll use in your Aembit configuration.

## Related

**Compatible trust providers**

* [AWS Role](../../trust-providers/aws-role-trust-provider.md)
