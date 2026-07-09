---
type: how-to
title: "AWS Lambda ARN"
description: "How to identify Client Workloads using AWS Lambda ARN for AWS Lambda deployments"
resource: https://docs.aembit.io/user-guide/access-policies/client-workloads/identification/aws-lambda-arn/
interface: web-ui
tags: [aws, identification, client-workload, access-policy]
timestamp: 2025-05-22T22:55:26-07:00
type_inferred: true
---

# AWS Lambda ARN

The AWS Lambda ARN Client Workload identification method is applicable only to AWS Lambda deployments. Aembit utilizes the native AWS identifier (Lambda ARN) to identify and distinguish Client Workloads.

## Applicable deployment type

[Section titled “Applicable deployment type”](#applicable-deployment-type)

This method is suitable for Aembit Edge-based deployments.

## Configuration

[Section titled “Configuration”](#configuration)

### Aembit Cloud

[Section titled “Aembit Cloud”](#aembit-cloud)

1. Create a new Client Workload.
2. Choose **AWS Lambda ARN** for client identification.
3. In the **Value** field, enter the AWS Lambda ARN. You must use the following format: `arn:aws:lambda:<region>:<account>:function:<lambda function name>`

### Using versions

[Section titled “Using versions”](#using-versions)

When working with AWS Lambda ARN, it’s crucial to understand the two types of ARNs: Qualified ARN and Unqualified ARN. Each serves a specific purpose, and understanding their differences is key. For detailed information, refer to the official [AWS Documentation](https://docs.aws.amazon.com/lambda/latest/dg/configuration-versions.html#versioning-versions-using).

**Unqualified ARN** - Used for the latest version of a Lambda function.

Example: `arn:aws:lambda:aws-region:acct-id:function:helloworld`

**Qualified ARN** - Used for a specific version of a Lambda function or [aliases](https://docs.aws.amazon.com/lambda/latest/dg/configuration-aliases.html).

Example:

`arn:aws:lambda:aws-region:acct-id:function:helloworld:42`

If you need to work with a Qualified ARN, you must create a Client Workload that uses a wildcard to handle multiple versions. For instance: `arn:aws:lambda:aws-region:acct-id:function:helloworld:*`.

### Finding the AWS Lambda ARN

[Section titled “Finding the AWS Lambda ARN”](#finding-the-aws-lambda-arn)

You can find the list of Lambda functions via the AWS CLI by executing: `aws lambda list-functions --region us-east-2`

This command will return all the Lambda-related information, including the Lambda ARN, which is available under the `FunctionArn` field.

## Related

**Compatible trust providers**

* [AWS Role](../../trust-providers/aws-role-trust-provider.md)
