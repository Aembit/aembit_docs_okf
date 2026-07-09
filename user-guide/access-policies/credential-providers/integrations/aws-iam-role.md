---
type: how-to
title: "Create a AWS IAM Role Integration for an AWS IAM Role"
description: "How to create an AWS IAM Role Credential Provider Integration using an AWS IAM Role"
resource: https://docs.aembit.io/user-guide/access-policies/credential-providers/integrations/aws-iam-role/
interface: web-ui
tags: [integration, credential-provider, access-policy]
timestamp: 2025-08-19T18:15:09-07:00
type_inferred: true
---

# Create a AWS IAM Role Integration for an AWS IAM Role

Aembit uses the AWS IAM Role Credential Provider Integration to enable you to retrieve credentials using the AWS IAM Role you specify.

This page details everything you need to create an AWS IAM Role Credential Provider Integration. This integration requires the use of an AWS IAM Role that has the necessary permissions to access the resources you want to manage with Aembit.

## Configure a AWS IAM Role integration

[Section titled “Configure a AWS IAM Role integration”](#configure-a-aws-iam-role-integration)

To create a AWS IAM Role integration, follow these steps:

1. Log into your Aembit Tenant, and go to **Credential Providers -> Integrations** in the left sidebar.

   ![Credential Provider - Integrations tab](https://docs.aembit.io/_astro/cp-integrations-page.Q7suvjMH_Z1SCKgo.webp)

2. (Optional) In the top right corner, select the [Resource Set](../../../administration/resource-sets/overview.md) that you want this Credential Provider Integration to reside.

3. Click **+ New**, which displays the **Integration** pop out menu.

4. Select **AWS IAM Role**.

5. Fill out the following fields on the **AWS IAM Role** form:

   * **Display Name** - Enter a unique name for this integration.

   * **Description** - (Optional) Enter a description.

6. In the **Configuration** section, enter the following information:

   * **AWS IAM Role ARN** - Enter the Amazon Resource Name (ARN) of the AWS IAM Role that you want to use for this integration. This role must have the necessary permissions to access the resources you want to manage with Aembit.

     AWS IAM Role ARN location

     You can find the ARN of an AWS IAM Role in the AWS Management Console under **IAM -> Roles**, then select the role you want to use. AWS displays the role ARN at the top of the role’s **Summary** page.

   * **Lifetime** - Specify the duration of the temporary AWS credentials which Aembit uses to access AWS resources (default: 3600 seconds).

   * **Populate Secret ARNs** - Enable this option to automatically populate the ARNs of the secrets accessible with the **AWS IAM Role ARN** you just entered in the **AWS Secrets Manager Secret Arn** field of Credential Providers that use this integration.

   The form should look similar to the following screenshot:

   ![Credential Provider Integration - AWS IAM Role form](https://docs.aembit.io/_astro/cp-integration-aws-iam-role.nl_PesOr_114MYk.webp)

7. Click **Save**.

   Aembit displays the new integration in the list of Credential Provider Integrations.

Once you’ve created the AWS IAM Role integration, Aembit displays it in the list of Credential Provider Integrations.

You can tell that you’ve configured the integration correctly if you see a green **Ready** badge in the **Status** column, like the following screenshot:

![Credential Provider - Integrations tab with new integration](https://docs.aembit.io/_astro/cp-integration-aws-iam-role-verify.DhmHJ759_1APmnF.webp)

## Next steps

[Section titled “Next steps”](#next-steps)

Now that you’ve created a AWS Secrets Manager Credential Provider Integration, create a [AWS Secrets Manager Value Credential Provider](../aws-secrets-manager.md) to use with your Server Workloads.
