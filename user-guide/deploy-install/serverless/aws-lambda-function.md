---
type: how-to
title: "Deploy Aembit Edge to AWS Lambda function"
description: "How to deploy Aembit Edge Components in an AWS Lambda function environment"
resource: https://docs.aembit.io/user-guide/deploy-install/serverless/aws-lambda-function/
interface: web-ui
tags: ["serverless", "deploy-install"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Deploy Aembit Edge to AWS Lambda function

Aembit provides many different deployment options which you can use to deploy Aembit Edge Components in your environment. Each of these options provides similar features and functionality; however, the steps for each of these options are specific to the deployment option you select.

This page describes the process to deploy Aembit Edge Components in zip-based (vs container-based) AWS Lambda functions using an [AWS Lambda layer](https://docs.aws.amazon.com/lambda/latest/dg/chapter-layers.html).

> **Note**
>
> For information on deploying Aembit Edge Components in AWS Lambda container environment, see [AWS Lambda Containers](aws-lambda-container.md).

## Deploy Aembit Edge Components

### Topology

Aembit deploys Agent Proxies for AWS Lambda functions as [AWS Lambda Layers](https://docs.aws.amazon.com/lambda/latest/dg/chapter-layers.html) which are automatically launched by the AWS Lambda Runtime.

### VPC

For each AWS region hosting your Lambda functions, you must create a Virtual Private Cloud (VPC) (or use an existing one). All Lambda functions in each AWS account or region that include Aembit components must connect to a corresponding VPC in the same region.

This VPC must provide:

* Access to Agent Controller.
* Access to the Internet.

Agent Controllers can either operate directly within this VPC or another location, but must be accessible from this VPC.

AWS Lambda functions using zip-based packaging must explicitly connect to a VPC subnet to enable Agent Proxy communication.

#### Ensuring internet access

Agent Proxy requires outbound internet access to communicate with Aembit Cloud. When you configure your Lambda function within a VPC, you must set up specific networking for internet access.

Place your Lambda function in a private subnet and route outbound traffic from this subnet through a NAT Gateway (Network Address Translation Gateway) located in a public subnet.

> **Note**
>
> * AWS assigns private IP addresses to Lambda functions in a VPC. An Internet Gateway (IGW) in a public subnet can’t directly route traffic from these private IPs to the public internet.
> * The NAT Gateway translates the necessary public IP addresses for your Lambda’s private IP traffic.

### Agent Controller

Deploy Agent Controller either on a [virtual machine](../virtual-machine/overview.md) or within your [Kubernetes cluster](../kubernetes/kubernetes.md).

### Lambda layer packaging

Aembit publishes the Aembit AWS Lambda Layer to the [AWS Serverless Application Repository (SAR)](https://serverlessrepo.aws.amazon.com/applications) which you can deploy into your AWS account.

To deploy the Aembit Lambda layer:

1. Navigate to **SAR** and search the public applications list for “Aembit Lambda layer”.

   Public **SAR** entry for [Aembit AWS Lambda Layer](https://serverlessrepo.aws.amazon.com/applications/us-east-1/833062290399/aembit-agent-proxy-lambda-layer).

2. Deploy via the **AWS Console** or **AWS CLI**.

3. Once deployed find the **Lambda Layer Version ARN** .

4. Attach the Aembit Layer to your function by doing the following:

   1. In the AWS Console, open your **Lambda function**.
   2. Under **Layers** section, click **Add a Layer**.
   3. Select **Provide a layer version ARN** and paste the ARN you retrieved.

### Lambda function configuration

To use the Aembit Lambda Layer in your Lambda functions:

* Specify additional environment variables for your Lambda function.

  For Agent Controllers with TLS configured:

  ```shell
  AEMBIT_AGENT_CONTROLLER=https://<AGENT_CONTROLLER_HOST>:5443
  ```

  > **Caution**
  >
  > To configure TLS Decrypt on your Agent Controller, see [Configuring TLS Decrypt](#configuring-tls-decrypt).

  For Agent Controllers without TLS:

  ```shell
  AEMBIT_AGENT_CONTROLLER=http://<AGENT_CONTROLLER_HOST>:5000
  ```

* Specify `http_proxy` and/or `https_proxy` environment variables to direct HTTP and/or HTTPS traffic through Aembit:

  ```shell
  http_proxy=http://localhost:8000
  https_proxy=http://localhost:8000
  ```

You can configure additional environment variables to set the Agent Proxy log level, among other settings. For details, see the [list of available Agent Proxy environment variables](../virtual-machine/overview.md).

## Client Workload identification

The most convenient way to identify Lambda function Client Workloads is to use the [AWS Lambda ARN Client Workload identification method](../../access-policies/client-workloads/identification/aws-lambda-arn.md).

> **Note**
>
> If you plan to work with a specific version of Lambda functions or aliases (as opposed to the latest version), you must use Qualified ARNs. For more details, see the Client Workload Identification article linked above.

Alternatively, you can use [Aembit Client ID](../../access-policies/client-workloads/identification/aembit-client-id.md) by setting the `CLIENT_WORKLOAD_ID` environment variable.

## Trust Providers

The only Trust Provider available for Lambda function Client Workloads is [AWS Role Trust Provider](../../access-policies/trust-providers/aws-role-trust-provider.md).

See [Lambda Support](../../access-policies/trust-providers/aws-role-trust-provider.md#lambda-support) for more details about the configuration.

## Resource Set Deployment

To deploy a Resource Set using an AWS Lambda function, you must specify the `AEMBIT_RESOURCE_SET_ID` environment variable in your Client Workload. Configuring this environment variable enables Agent Proxy to support Client Workloads in the Resource Set you specify.

## Lambda lifecycle and Workload Events

Lambda functions that use zip-based packaging don’t support long-lived container instances in the same way as Lambda containers. As a result, workload events that Agent Proxy generates may not transmit immediately.

Agent Proxy buffers these events in memory and attempts to transmit them either:

* At the end of the function invocation.
* On subsequent invocations (if the Lambda instance is reused).

If the Lambda is frequently cold-started, it’s possible that it may delay or drop some events. In practice, AWS reuses Lambda instances under normal load conditions, so buffering is often sufficient.

## Configuring TLS Decrypt

To enable TLS decryption, download the Aembit Tenant certificate from the Aembit UI and add it to either a Lambda Layer attached to your function or directly to your function package.

Due to the read-only filesystem in Lambda functions, Aembit recommends the following these steps:

1. Create a `rootCA.pem` certificate bundle that includes:

   * Commonly trusted certificate authorities appropriate for your environment
   * Your Aembit Tenant root CA, available at `https://$<AEMBIT_TENANT_ID>.aembit.io/api/v1/root-ca`

2. Add an environment variable to indicate the location of the certificate bundle.

   * When packaging the certificate bundle in a **Lambda Layer**:

     ```shell
     SSL_CERT_FILE=/opt/rootCA.pem
     ```

   * When packaging the certificate bundle with your **Lambda function**:

     ```shell
     SSL_CERT_FILE=/var/task/rootCA.pem
     ```

To configure TLS Decrypt, see [Configure TLS Decrypt](../advanced-options/tls-decrypt/configure-tls-decrypt.md).

## Performance

The startup and shutdown times for Agent Proxy normally take several seconds, which results in an increase in the execution time of your Lambda function by several seconds.

## Limitations

Aembit supports only the following protocols in AWS Lambda function environments:

* HTTP
* HTTPS
* Snowflake

## Supported phases

The Aembit AWS Lambda layer supports credential injection during the following Lambda lifecycle phase:

[INIT phase](https://docs.aws.amazon.com/lambda/latest/dg/lambda-runtime-environment.html#runtimes-lifecycle-invoke): Fully supported.

> **Note**
>
> Unlike Lambda Containers, zip-based Lambda functions don’t have an INIT phase that the Lambda function exposes separately for external extensions. The Aembit Layer relies on the Lambda function’s runtime execution to launch Agent Proxy during the invocation phase.
