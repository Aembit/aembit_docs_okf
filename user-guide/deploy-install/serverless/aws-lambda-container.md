---
type: how-to
title: "Deploy Aembit Edge to AWS Lambda Container"
description: "How to deploy Aembit Edge Components in an AWS Lambda container environment"
resource: https://docs.aembit.io/user-guide/deploy-install/serverless/aws-lambda-container/
interface: web-ui
tags: ["serverless", "deploy-install"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Deploy Aembit Edge to AWS Lambda Container

Aembit provides many different deployment options which you can use to deploy Aembit Edge Components in your environment. Each of these options provides similar features and functionality; however, the steps for each of these options are specific to the deployment option you select.

This page describes the process to deploy Aembit Edge Components to an [AWS Lambda container](https://docs.aws.amazon.com/prescriptive-guidance/latest/patterns/deploy-lambda-functions-with-container-images.html) environment.

> **Note**
>
> For information on deploying Aembit Edge Components for zip-based AWS Lambda functions, see [AWS Lambda Functions](aws-lambda-function.md).

## Deploy Aembit Edge Components

### Topology

Aembit deploys Agent Proxies for AWS Lambda containers within Lambda Containers, packaged as [AWS Lambda Extensions](https://docs.aws.amazon.com/lambda/latest/dg/lambda-extensions.html). The AWS Lambda Runtime automatically launches them.

The deployed Lambda function must connect to an Amazon Virtual Private Cloud (VPC) with access to both the Agent Controller and the Internet.

### VPC

For each AWS region hosting your Lambda containers, you must create a VPC (or use an existing one). All Lambda containers in each AWS account/region that include Aembit components must connect to a corresponding VPC in the same region.

This VPC must provide:

* Access to the Agent Controller.
* Access to the Internet.

Agent Controllers can either operate directly within this VPC or elsewhere, but must be accessible from this VPC.

#### Ensuring internet access

Agent Proxy requires outbound internet access to communicate with Aembit Cloud. When you configure your Lambda function within a VPC, you must set up specific networking for internet access.

Place your Lambda function in a private subnet and route outbound traffic from this subnet through a NAT Gateway (Network Address Translation Gateway) located in a public subnet.

> **Note**
>
> * AWS assigns private IP addresses to Lambda functions in a VPC. An Internet Gateway (IGW) in a public subnet can’t directly route traffic from these private IPs to the public internet.
> * The NAT Gateway translates the necessary public IP addresses for your Lambda’s private IP traffic.

### Agent Controller

Deploy the Agent Controller either on a [Virtual Machine](../virtual-machine/overview.md) or within your [Kubernetes Cluster](../kubernetes/kubernetes.md).

### Lambda container packaging

Aembit distributes Edge Components as part of the Aembit AWS Lambda Extension. Aembit incorporates all Lambda extensions into Lambda containers at build time.

Include the following commands in your Dockerfile to add the extension to your AWS Lambda container image, replacing `<version>` with the current `aembit_aws_lambda_extension` version available on [Docker Hub](https://hub.docker.com/r/aembit/aembit_aws_lambda_extension/tags).

```dockerfile
COPY --from=aembit/aembit_aws_lambda_extension:<version> /extension/ /opt/extensions
```

> **Security best practice**
>
> Always [verify Aembit container image signatures](../verify-container-images.md#aws-lambda-extension) to validate the authenticity of the container images you use from Aembit.

### Lambda container deployment

Deploy or update your Lambda container:

* Specify additional environment variables for your Lambda function.

  For Agent Controllers with TLS configured:

  ```shell
  AEMBIT_AGENT_CONTROLLER=https://<AGENT_CONTROLLER_HOST>:5443
  ```

  For Agent Controllers without TLS:

  ```shell
  AEMBIT_AGENT_CONTROLLER=http://<AGENT_CONTROLLER_HOST>:5000
  ```

* Specify `http_proxy` and/or `https_proxy` environment variables to direct HTTP and/or HTTPS traffic through Aembit:

  ```shell
  http_proxy=http://localhost:8000
  https_proxy=http://localhost:8000
  ```

You can configure additional environment variables to set the Agent Proxy log level, among other settings. See [Agent Proxy environment variables](../virtual-machine/overview.md) for the full list.

## Client Workload identification

The most convenient way to identify Lambda container Client Workloads is using [AWS Lambda ARN Client Workload Identification](../../access-policies/client-workloads/identification/aws-lambda-arn.md).

> **Note**
>
> If you plan to work with a specific version of a Lambda function or aliases (as opposed to the latest version), you must use Qualified ARNs. For more details, see [AWS Lambda ARN Client Workload Identification](../../access-policies/client-workloads/identification/aws-lambda-arn.md).

Alternatively, you can use [Aembit Client ID](../../access-policies/client-workloads/identification/aembit-client-id.md) by setting the `CLIENT_WORKLOAD_ID` environment variable.

## Trust Providers

The only Trust Provider available for Lambda containers Client Workloads is [AWS Role Trust Provider](../../access-policies/trust-providers/aws-role-trust-provider.md).

See [Lambda Support](../../access-policies/trust-providers/aws-role-trust-provider.md#lambda-support) for more details about the configuration.

## Resource Set deployment

To deploy a Resource Set using an AWS Lambda Container, you need to specify the `AEMBIT_RESOURCE_SET_ID` environment variable in your Client Workload. This configuration enables the Agent Proxy to support Client Workloads in this Resource Set.

## Lambda Container lifecycle and workload events

AWS pauses Lambda Containers immediately after the completion of the Lambda function. As a result, Agent Proxy may not have enough time to send workload events to Aembit Cloud. Agent Proxy retains workload events and sends them either at the next Lambda function invocation or during the container shutdown process.

As a result, it may take longer than in other environments for these workload events to become available in your Aembit Tenant.

## Configuring TLS Decrypt

To use TLS Decrypt in your AWS Lambda container, download the tenant certificate, and trust it within your AWS Lambda container. As the Lambda container uses a read-only filesystem, Aembit recommends including this step in your build pipeline.

Refer to the [Configure TLS Decrypt](../advanced-options/tls-decrypt/configure-tls-decrypt.md) page for comprehensive instructions on configuring TLS Decrypt.

## Performance

The startup and shutdown times for the Aembit Agent Proxy normally take a few seconds, which adds a few seconds to the execution time of your Lambda function.

## Limitations

Aembit supports only the following protocols in AWS Lambda container environments:

* HTTP
* HTTPS
* Snowflake

## Supported phases

The Aembit AWS Lambda Extension supports Client Workload identification and credential injection during the following Lambda container lifecycle phases:

* [INIT phase](https://docs.aws.amazon.com/lambda/latest/dg/lambda-runtime-environment.html#runtimes-lifecycle-invoke) Supported for internal extensions, function inits, and external extensions executed after the Aembit extension.
* [INVOKE phase](https://docs.aws.amazon.com/lambda/latest/dg/lambda-runtime-environment.html#runtimes-lifecycle-ib) Fully supported.
