---
type: how-to
title: "AWS Role Trust Provider"
description: "This page describes the steps needed to configure the AWS Role Trust Provider."
resource: https://docs.aembit.io/user-guide/access-policies/trust-providers/aws-role-trust-provider/
interface: web-ui
tags: [trust-provider, access-policy]
timestamp: 2025-05-22T22:55:26-07:00
type_inferred: true
---

# AWS Role Trust Provider


The AWS Role Trust Provider supports attestation within the AWS environment. Aembit Edge Components can currently be deployed in several AWS services that support AWS Role Trust Provider attestation:

* EC2 instances with an attached IAM role
* AWS Role instances
* ECS Fargate containers
* Lambda containers

## Match rules

[Section titled “Match rules”](#match-rules)

The following match rules are available for this Trust Provider type:

* `accountId`
* `assumedRole`
* `roleArn`
* `username`

For a description of the match rule fields available in the AWS Role Trust Provider, please refer to the [AWS documentation](https://docs.aws.amazon.com/STS/latest/APIReference/API_GetCallerIdentity.html).

## AWS Role support

[Section titled “AWS Role support”](#aws-role-support)

Aembit supports AWS Role-Based Trust Providers by enabling you to create a new Trust Provider using the Aembit Tenant UI. Follow the steps below to create the AWS Role Trust Provider.

1. On the Trust Providers page, click on the **New** button to open the Trust Providers dialog window.

2. In the dialog window, enter the following information:

* **Name** - The name of the Trust Provider
* **Description** - An optional text description for the Trust Provider
* **Trust Provider** - A drop-down menu that lists the different Trust Provider types

3. Select **AWS Role** from the Trust Provider drop-down menu.

4. Click on the **Match Rules** link to open an instance of the Match Rules drop-down menu.

* If you use the `roleARN` value, make sure it is in the following format: `arn:aws:sts::<ACCOUNT_ID>:assumed-role/<ROLE_NAME>/<EC2_INSTANCE_ID>`

* If you use the `username` value, make sure it is in the following format: `<USER_NAME>:<EC2_INSTANCE_ID>`

Note

The username value refers to the `AccessKeyId` field in Amazon’s [IAM Roles for Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-roles-for-amazon-ec2.html#instance-metadata-security-credentials) documentation.

![Trust Provider Dialog Window -
Complete](https://docs.aembit.io/_astro/trust_providers_new_trust_provider_dialog_window_complete.BhLqwfZ0_ZOSkmU.webp)

5. Click **Save** when finished. Your new EC2 Trust Provider will appear on the main Trust Providers page.

## ECS Fargate container support

[Section titled “ECS Fargate container support”](#ecs-fargate-container-support)

You must assign an AWS IAM role with `AmazonECSTaskExecutionRolePolicy` permission to your ECS tasks.

Note

You have different ways to perform the following steps (e.g. UI, API, CDK, Terraform, etc.). The following steps are one approach; however, select the way that is most appropriate for your organization.

1. Check the existence of AWS IAM ecsTaskExecutionRole. Please refer to the [AWS documentation](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_execution_IAM_role.html#procedure_check_execution_role) for more information.

2. Create AWS IAM `ecsTaskExecutionRole` if this is missing. Please refer to the [AWS documentation](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_execution_IAM_role.html#create-task-execution-role) for more information.

3. Retrieve the ARN of `ecsTaskExecutionRole` role. This should look like `arn:aws:iam::<account_id>:role/ecsTaskExecutionRole`

4. Assign this role in your ECS task definition by setting the task role and task execute role fields.

![ECS Role Trust Provider Page](https://docs.aembit.io/_astro/ecs_task_role.DHKGsPm6_d5pbv.webp)

## Lambda support

[Section titled “Lambda support”](#lambda-support)

If you are using this Trust Provider for attestation of workloads running in a Lambda environment, you may use the following match rules:

* `accountId`
* `roleArn`

The Lambda **roleArn** is structured as follows:

```shell
arn:aws:sts::<accountId>:assumed-role/<functionRoleName>/<functionName>
```

## Related

**Compatible credential providers**

* [API Key](../credential-providers/api-key.md)
* [AWS STS Federation](../credential-providers/aws-security-token-service-federation.md)
* [HashiCorp Vault Client Token](../credential-providers/vault-client-token.md)
* [JSON Web Token (JWT)](../credential-providers/json-web-token.md)
* [OAuth 2.0 Client Credentials](../credential-providers/oauth-client-credentials.md)
* [OIDC ID Token](../credential-providers/oidc-id-token.md)
* [Username & Password](../credential-providers/username-password.md)

**Client workload identifiers this trust supports**

* [Aembit Client ID](../client-workloads/identification/aembit-client-id.md)
* [AWS Account ID](../client-workloads/identification/aws-account-id.md)
* [AWS EC2 Instance ID](../client-workloads/identification/aws-ec2-instance-id.md)
* [AWS ECS Service Name](../client-workloads/identification/aws-ecs-service-name.md)
* [AWS ECS Task Family](../client-workloads/identification/aws-ecs-task-family.md)
* [AWS Lambda ARN](../client-workloads/identification/aws-lambda-arn.md)
* [AWS Region](../client-workloads/identification/aws-region.md)
* [Hostname](../client-workloads/identification/hostname.md)
* [Process Command Line](../client-workloads/identification/process-command-line.md)
* [Process Name](../client-workloads/identification/process-name.md)
* [Process Path](../client-workloads/identification/process-path.md)
* [Process User Name](../client-workloads/identification/process-user-name.md)
* [Source IP Address](../client-workloads/identification/source-ip.md)
