---
type: how-to
title: "Create an AWS Server Workload"
description: "How to configure Aembit to work with AWS Cloud services using STS federation and SigV4 authentication"
resource: https://docs.aembit.io/user-guide/access-policies/server-workloads/guides/aws-cloud/
interface: web-ui
tags: ["guide", "server-workload", "access-policy"]
timestamp: 2026-09-22T15:17:29-07:00
---

# Create an AWS Server Workload

This guide walks you through creating a Server Workload in Aembit to securely access AWS services without storing static AWS credentials.

**Use this Server Workload** to enable your applications to authenticate to AWS services such as S3, Lambda, EC2, DynamoDB, SQS, and other AWS API endpoints.

Aembit authenticates to AWS using a Credential Provider that performs [AWS Security Token Service (STS) federation](../../credential-providers/aws-security-token-service-federation.md) with [SigV4 and SigV4a](../../credential-providers/aws-sigv4.md) request signing.

## Prerequisites

Before you begin, ensure you have the following:

* **Account access** - Access to your Aembit Tenant (role: Workload Administrator or higher), and access to the AWS Console with permissions to create IAM Roles and Identity Providers

* **Infrastructure** - Aembit Edge Components deployed in your environment:

  * Agent Proxy installed, through the [Linux](../../../deploy-install/virtual-machine/linux/agent-proxy-install-linux.md) or [Windows](../../../deploy-install/virtual-machine/windows/agent-proxy-install-windows.md) installation for VMs, or the [Kubernetes deployment](../../../deploy-install/kubernetes/overview.md)
  * [TLS Decrypt](../../../deploy-install/advanced-options/tls-decrypt/configure-tls-decrypt.md) configured on your Agent Proxy. AWS API requests require TLS Decrypt because Agent Proxy must inspect HTTPS traffic to inject SigV4 signatures. TLS decryption occurs only on the Agent Proxy running alongside your workload.
  * Network connectivity from your workload to AWS service endpoints (outbound HTTPS to `*.amazonaws.com`)

* **AWS configuration** - An IAM Role configured in AWS with the necessary permissions to access the desired AWS services

## How Aembit authenticates to AWS

Aembit uses AWS STS federation to obtain temporary credentials, then signs requests using AWS SigV4 or SigV4a.

![How Aembit authenticates to AWS using STS federation and SigV4 signing](https://docs.aembit.io/d2/docs/user-guide/access-policies/server-workloads/guides/aws-cloud-0.svg)

Aembit automatically selects the appropriate signing protocol:

* **SigV4** for regional AWS services (when the hostname includes a region like `us-east-1`)
* **SigV4a** for global or multi-region services (when the hostname doesn’t include a region)

For details on how Aembit handles AWS request signing, see [How Aembit uses AWS SigV4 and SigV4a](../../credential-providers/aws-sigv4.md).

## Server Workload configuration

Select the tab for the AWS service you want to configure:

* Generic

  Use this configuration for most AWS services that follow the standard regional endpoint pattern, such as Lambda, SQS, DynamoDB, and Key Management Service (KMS).

  > **Note**
  >
  > This generic pattern isn’t directly applicable to Amazon S3, which uses a different endpoint structure with bucket names as subdomains. For S3, use the **S3** tab instead. For more information about AWS service endpoint patterns, see [AWS service endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html).

  1. Log in to your Aembit Tenant.

  2. Go to **Server Workloads**, and click **+ New**.

  3. Configure the following fields:

     * **Name**: Enter a descriptive name (for example, `aws-generic`)
     * **Host**: `*.amazonaws.com`
     * **Application Protocol**: HTTP
     * **Port**: `443`
     * **Forward to Port**: `443` with TLS enabled
     * **Authentication method**: HTTP Authentication
     * **Authentication scheme**: AWS Signature v4

     > **Scope**
     >
     > Using `*.amazonaws.com` enables you to reuse this Server Workload across multiple AWS services without having to determine the service or region-specific hostname. If you prefer a granularly scoped Server Workload, specify a specific hostname, for example `kms.us-east-1.amazonaws.com` or `lambda.us-west-2.amazonaws.com`.

  4. Click **Save**.

* S3

  Use this configuration for Amazon S3.

  > **Known limitation**
  >
  > **Request compression:** The Agent Proxy doesn’t support streaming payload signing when the HTTP request body uses content encodings. If you have request compression enabled, turn it off by setting `AWS_DISABLE_REQUEST_COMPRESSION=true`.
  >
  > For all limitations and workarounds, see [Known limitations](../../credential-providers/aws-sigv4.md#known-limitations).

  Amazon S3 uses a unique endpoint pattern where bucket names appear as subdomains.

  1. Log in to your Aembit Tenant.

  2. Go to **Server Workloads**, and click **+ New**.

  3. Configure the following fields:

     * **Name**: Enter a descriptive name (for example, `aws-s3`)

     * **Host**: Choose one of the following options:

       | Host Value                                | Scope                                                                                        |
       | ----------------------------------------- | -------------------------------------------------------------------------------------------- |
       | `*.s3.<region>.amazonaws.com`             | All S3 buckets in a specific region (for example, `*.s3.us-east-1.amazonaws.com`)            |
       | `<bucket-name>.s3.<region>.amazonaws.com` | A specific bucket in a specific region (for example, `my-bucket.s3.us-east-1.amazonaws.com`) |

     * **Application Protocol**: HTTP

     * **Port**: `443`

     * **Forward to Port**: `443` with TLS enabled

     * **Authentication method**: HTTP Authentication

     * **Authentication scheme**: AWS Signature v4

     > **When to use SigV4a**
     >
     > Aembit automatically selects between AWS Signature v4 (SigV4) and Signature v4a (SigV4a) based on the hostname. Use SigV4a for S3 Multi-Region Access Points or other global S3 services that span multiple regions. For more information, see [How Aembit uses AWS SigV4 and SigV4a](../../credential-providers/aws-sigv4.md).

  4. Click **Save**.

* EC2

  Use this configuration for Amazon EC2 (Elastic Compute Cloud).

  1. Log in to your Aembit Tenant.

  2. Go to **Server Workloads**, and click **+ New**.

  3. Configure the following fields:

     * **Name**: Enter a descriptive name (for example, `aws-ec2`)

     * **Host**: Choose one of the following options:

       | Host Value                   | Scope                                                                 |
       | ---------------------------- | --------------------------------------------------------------------- |
       | `ec2.<region>.amazonaws.com` | EC2 in a specific region (for example, `ec2.us-west-2.amazonaws.com`) |
       | `ec2.*.amazonaws.com`        | EC2 in any region (wildcard)                                          |

     * **Application Protocol**: HTTP

     * **Port**: `443`

     * **Forward to Port**: `443` with TLS enabled

     * **Authentication method**: HTTP Authentication

     * **Authentication scheme**: AWS Signature v4

     > **Regional endpoints**
     >
     > AWS EC2 is a regional service. If you specify the global endpoint (`ec2.amazonaws.com`), AWS routes requests to `us-east-1` by default. For other regions, use the regional endpoint format `ec2.<region>.amazonaws.com`.

  4. Click **Save**.

## Credential Provider configuration

1. Create an AWS IAM Role in AWS with the necessary permissions to access the desired AWS services. Then, create an AWS IAM Role Integration in your Aembit Tenant. See [Create an AWS IAM Role Integration](../../credential-providers/integrations/aws-iam-role.md).

2. Create an AWS Security Token Service (STS) Federation Credential Provider. See [Configure an AWS STS Federation Credential Provider](../../credential-providers/aws-security-token-service-federation.md).

## Access Policy configuration

Create an Access Policy linking your Client Workload, the AWS STS Federation Credential Provider, and the Server Workload. See [Access Policies](../../overview.md) for details.

## Client Workload configuration

Aembit handles the credentials required to access AWS services, eliminating the need for you to manage them directly. Remove any previously used AWS credentials (access keys, secret keys) from your Client Workload.

If you access AWS through an SDK or library, the SDK may still require credentials to be present for initialization purposes. In this scenario, provide placeholder credentials. Aembit replaces these placeholder credentials with real temporary credentials during the access request. For more information, see [Understanding placeholder credentials](../../../../dev-guide/integration/client-library-patterns.md#understanding-placeholder-credentials).

```shell
# Placeholder credentials for SDK initialization
export AWS_ACCESS_KEY_ID=placeholder
export AWS_SECRET_ACCESS_KEY=placeholder
```

If you’re using the [AWS CLI](https://aws.amazon.com/cli/), set the `AWS_CA_BUNDLE` environment variable to point to your Aembit Tenant Root CA certificate:

```shell
export AWS_CA_BUNDLE=/path/to/aembit-root-ca.pem
```

## Test the integration

After completing the full configuration (Server Workload, Credential Provider, Client Workload, and Access Policy), verify access using the AWS CLI.

* Generic (KMS example)

  ```shell
  # Verify credentials are working
  aws sts get-caller-identity


  # List KMS keys
  aws kms list-keys


  # Describe a specific key
  aws kms describe-key --key-id <your-key-id>
  ```

* S3

  ```shell
  # Verify credentials are working
  aws sts get-caller-identity


  # List all S3 buckets
  aws s3 ls


  # List contents of a specific bucket
  aws s3 ls s3://<your-bucket>


  # Download a file from S3
  aws s3 cp s3://<your-bucket>/<file> ./


  # Upload a file to S3
  aws s3 cp ./local-file.txt s3://<your-bucket>/
  ```

* EC2

  ```shell
  # Verify credentials are working
  aws sts get-caller-identity


  # List all EC2 instances
  aws ec2 describe-instances


  # List instances with specific filters
  aws ec2 describe-instances --filters "Name=instance-state-name,Values=running"


  # Describe available regions
  aws ec2 describe-regions
  ```

## Common configuration

### IAM permissions

AWS IAM policies require different Resource ARN formats depending on the operation:

| Operation Type                      | Resource ARN Format          | Example                    |
| ----------------------------------- | ---------------------------- | -------------------------- |
| Bucket-level (ListBucket)           | `arn:aws:s3:::bucket-name`   | `arn:aws:s3:::my-bucket`   |
| Object-level (GetObject, PutObject) | `arn:aws:s3:::bucket-name/*` | `arn:aws:s3:::my-bucket/*` |

Example IAM policy for S3 access

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["s3:ListBucket"],
      "Resource": "arn:aws:s3:::my-bucket"
    },
    {
      "Effect": "Allow",
      "Action": ["s3:GetObject", "s3:PutObject"],
      "Resource": "arn:aws:s3:::my-bucket/*"
    }
  ]
}
```

For more on AWS IAM policies, see [Policies and permissions in Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/userguide/access-policy-language-overview.html).

### Regional endpoints

AWS services use different endpoint patterns:

| Service Type        | Endpoint Pattern                 | Example                                |
| ------------------- | -------------------------------- | -------------------------------------- |
| Regional services   | `service.region.amazonaws.com`   | `kms.us-east-1.amazonaws.com`          |
| Global services     | `service.amazonaws.com`          | `iam.amazonaws.com`                    |
| S3 (virtual-hosted) | `bucket.s3.region.amazonaws.com` | `my-bucket.s3.us-east-1.amazonaws.com` |

For the complete list of AWS service endpoints, see [AWS service endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html).

### Credential lifecycle

Aembit dynamically generates short-lived AWS STS credentials, eliminating manual credential rotation. For details on credential rotation, compromise response, and audit logging, see [Credential Lifecycle Management](../credential-lifecycle.md).

## Troubleshooting

For common issues like Agent Proxy connectivity, network problems, or TLS configuration, see the [Troubleshooting Guide](../troubleshooting.md).

### AWS-specific issues

#### Access denied errors

If you receive `AccessDenied` errors when accessing AWS services:

1. Verify your IAM Role has the correct permissions for the operation
2. Check that bucket-level and object-level permissions use the correct ARN format
3. Confirm the IAM Role trust policy allows the Aembit OIDC provider

#### Signature mismatch errors

If you receive signature mismatch errors:

1. Verify you configured [TLS Decrypt](../../../deploy-install/advanced-options/tls-decrypt/configure-tls-decrypt.md)
2. Check the `AWS_CA_BUNDLE` environment variable points to the Aembit Root CA certificate
3. If using request compression, turn it off with `AWS_DISABLE_REQUEST_COMPRESSION=true`

## Cleanup

Remove the AWS Server Workload

If you no longer need this integration, remove components in this order:

> **Deactivate Access Policies first**
>
> You must deactivate any Access Policies that reference the Server Workload or Credential Provider before you can delete those components. Attempting to delete a Server Workload or Credential Provider that’s in use by an Access Policy results in an error.

1. **Deactivate associated Access Policies** -

   * Go to **Access Policies**
   * Find the Access Policies that use this Server Workload or Credential Provider
   * Deactivate each Access Policy (toggle off)

2. **Delete the Server Workload in Aembit** -

   * Go to **Server Workloads**
   * Select your AWS workload and click **Delete**

3. **Delete the Credential Provider in Aembit** -

   * Go to **Credential Providers**
   * Select the associated AWS STS Federation Credential Provider and click **Delete**

4. **Delete the AWS IAM Role Integration in Aembit** -

   * Go to **Integrations**
   * Select the AWS IAM Role Integration and click **Delete**

5. **Delete AWS resources (optional)** -

   * AWS Console: **IAM** -> **Identity providers** -> Select the Aembit OIDC provider -> **Delete**
   * AWS Console: **IAM** -> **Roles** -> Select the IAM Role -> **Delete**

Deleting the Server Workload immediately stops credential provisioning. Ensure no applications are actively using this workload before deletion.

## Related resources

* [How Aembit uses AWS SigV4 and SigV4a](../../credential-providers/aws-sigv4.md) - Understanding AWS request signing
* [AWS STS Federation Credential Provider](../../credential-providers/aws-security-token-service-federation.md) - Detailed Credential Provider setup
* [AWS IAM Role Integration](../../credential-providers/integrations/aws-iam-role.md) - IAM Role configuration
* [Credential Lifecycle Management](../credential-lifecycle.md) - How Aembit manages credential rotation and security
* [Integrate through Agent Proxy](../../../../dev-guide/integration/agent-proxy.md) - Integration patterns and placeholder credentials
* [TLS Decrypt Configuration](../../../deploy-install/advanced-options/tls-decrypt/configure-tls-decrypt.md) - HTTPS interception setup
