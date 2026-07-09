---
type: how-to
title: "AWS ECS Task Family"
description: "How to identify AWS ECS Fargate workloads using the task family identifier within Aembit"
resource: https://docs.aembit.io/user-guide/access-policies/client-workloads/identification/aws-ecs-task-family/
interface: web-ui
tags: [aws, identification, client-workload, access-policy]
timestamp: 2025-05-29T11:26:12-07:00
type_inferred: true
---

# AWS ECS Task Family

This page explains how to use the **AWS ECS task family** identifier to uniquely identify workloads deployed on **AWS ECS Fargate**. The task family is a key identifier for defining and managing your ECS tasks.

## Understanding the AWS ECS task family identifier

[Section titled “Understanding the AWS ECS task family identifier”](#understanding-the-aws-ecs-task-family-identifier)

When deploying applications to AWS ECS Fargate, the task family provides a logical grouping and versioning mechanism. Each ECS task definition belongs to a specific task family. Refer to the [official AWS documentation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ecs-taskdefinition.html?utm_source=chatgpt.com) for additional details.

## Applicable deployment type

[Section titled “Applicable deployment type”](#applicable-deployment-type)

Aembit specifically designed the ECS Task Family identification method for Edge-based deployments on [AWS ECS Fargate](../../../deploy-install/serverless/aws-ecs-fargate.md).

## Create a Client Workload with an AWS ECS task family identifier

[Section titled “Create a Client Workload with an AWS ECS task family identifier”](#create-a-client-workload-with-an-aws-ecs-task-family-identifier)

To configure a Client Workload with an AWS ECS task family identifier, follow these steps:

1. Log into your Aembit Tenant.

2. Click **Client Workloads** in the left nav pane.

3. Click **New**, revealing the **Client Workload** pop out menu.

4. Enter the **Name** and optional **Description** for the Client Workload.

5. Under **Client Identification**, select **AWS ECS Task Family**.

   For **Value**, enter the task family name (without the revision) you have configured in AWS ECS.

   For example, if the task definition is `my-fargate-app:1` in the AWS ECS Console, enter `my-fargate-app` in the **Value** field.

   If you don’t know the task family name or how to find it, see [Find task family name in AWS ECS](#find-task-family-name-in-aws-ecs).

6. Click **Save**.

   Aembit displays the new Client Workload on the **Client Workloads** page.

## Find task family name in AWS ECS

[Section titled “Find task family name in AWS ECS”](#find-task-family-name-in-aws-ecs)

To find the task family name in the AWS ECS Console, follow these steps:

Note

You may see the same task family in other locations within the AWS Console.

Keep in mind that a full task definition includes both the task family name and the task definition revision (for example, `my-fargate-app:1`). In your Client Workload configuration in your Aembit Tenant, use only the task family name (for example, `my-fargate-app`).

1. **Open your AWS ECS Console**

   Open the AWS Management Console and go to the Elastic Container Service (ECS).

2. **Find your Task Definition**

   In the ECS console, go to **Task Definitions** in the left menu.

3. **Locate the Task Family**

   The **Task definition** column displays the task family name.

   This is the string you’ll use in your Aembit configuration.

## Related

**Compatible trust providers**

* [AWS Role](../../trust-providers/aws-role-trust-provider.md)
