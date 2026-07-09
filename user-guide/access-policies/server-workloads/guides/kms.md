---
type: how-to
title: "AWS Key Management Service (KMS)"
description: "This page describes how to configure Aembit to work with the AWS KMS server workload."
resource: https://docs.aembit.io/user-guide/access-policies/server-workloads/guides/kms/
interface: web-ui
tags: [security, guide, server-workload, access-policy]
timestamp: 2025-05-29T11:26:12-07:00
type_inferred: true
---

# AWS Key Management Service (KMS)


[Amazon Key Management Service](https://aws.amazon.com/kms/) is a service that enables you to create and control the encryption keys used to secure your data. This service integrates seamlessly with other AWS services, allowing you to easily encrypt and decrypt data, manage access to keys, and audit key usage.

Below you can find the Aembit configuration required to work with AWS KMS as a Server Workload using the AWS CLI, AWS SDK, or other HTTP-based client.

## Prerequisites

[Section titled “Prerequisites”](#prerequisites)

* You will need an AWS IAM role configured to access AWS KMS resources.

## Server Workload Configuration

[Section titled “Server Workload Configuration”](#server-workload-configuration)

1. Create a new Server Workload.

* **Name** - Choose a user-friendly name.

2. Configure the service endpoint:

* **Host** - `kms.us-east-1.amazonaws.com` (substitute **us-east-1** with your preferred region)
* **Application Protocol** - HTTP
* **Port** - 443 with TLS
* **Forward to Port** - 443 with TLS
* **Authentication method** - HTTP Authentication
* **Authentication scheme** - AWS Signature v4

## Credential Provider Configuration

[Section titled “Credential Provider Configuration”](#credential-provider-configuration)

1. Create a new Credential Provider.

* **Name** - Choose a user-friendly name.
* **Credential Type** - [AWS Security Token Service Federation](../../credential-providers/aws-security-token-service-federation.md)
* **OIDC Issuer URL** - Copy and securely store for later use in AWS Identity Provider configuration.
* **AWS IAM Role Arn** - Provide the IAM Role Arn.
* **Aembit IdP Token Audience** - Copy and securely store for later use in AWS Identity Provider configuration.

2. Create an AWS IAM Role to access KMS and trust Aembit.

* Within the AWS Console, go to **IAM** > **Identity providers** and select **Add provider**.

* On the Configure provider screen, complete the steps and fill out the values specified:

  * **Provider type** - Select **OpenID Connect**
  * **Provider URL** - Paste in the **OIDC Issuer URL** from the previous steps.
  * Click **Get thumbprint** to configure the AWS Identity Provider trust relationship.
  * **Audience** - Paste in the **Aembit IdP Token Audience** from the previous steps.
  * Click **Add provider**.

* Within the AWS Console, go to **IAM** > **Identity providers** and select the Identity Provider you just created.

* Click the **Assign role** button and choose **Use an existing role**.

## Client Workload Configuration

[Section titled “Client Workload Configuration”](#client-workload-configuration)

Aembit now handles the credentials required to access the Server Workload, eliminating the need for you to manage them directly. You can safely remove any previously used credentials from the Client Workload.

If you access the Server Workload through an SDK or library, it is possible that the SDK/library may still require credentials to be present for initialization purposes. In this scenario, you can provide placeholder credentials. Aembit will overwrite these placeholder credentials with the appropriate ones during the access process.

## Access Policy

[Section titled “Access Policy”](#access-policy)

* Create an access policy for a Client Workload to access the KMS Server Workload and assign the newly created Credential Provider to it.

## Required Features

[Section titled “Required Features”](#required-features)

* You will need to configure the [TLS Decrypt](../../../deploy-install/advanced-options/tls-decrypt/configure-tls-decrypt.md) feature to work with the KMS Server Workload.
* If you are using [AWS CLI](https://aws.amazon.com/cli/) to access KMS, you will need to set the environment variable `AWS_CA_BUNDLE` to point to the above certificate.

## Related

**Compatible credential providers**

* [AWS STS Federation](../../credential-providers/aws-security-token-service-federation.md)
