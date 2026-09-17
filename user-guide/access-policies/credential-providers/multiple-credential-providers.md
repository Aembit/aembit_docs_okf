---
type: explanation
title: "Configure multiple Credential Providers"
description: "Overview of configuring multiple Credential Providers in a single Access Policy"
resource: https://docs.aembit.io/user-guide/access-policies/credential-providers/multiple-credential-providers/
tags: ["credential-provider", "access-policy"]
timestamp: 2026-09-16T18:21:40-07:00
---

# Configure multiple Credential Providers

Some scenarios require multiple Credential Providers in a single Access Policy. For example, you might need different credentials for different users accessing the same Server Workload, or different IAM roles for accessing different AWS services.

This page provides an overview of multiple Credential Provider support. For configuration procedures, see the type-specific documentation in the following sections.

## Supported Credential Provider types

You can add multiple Credential Providers of the following types to a single Access Policy:

| Type                                                                                                                           | Selector mechanism                                              |
| ------------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------- |
| **[AWS STS Credential Providers (STS)](aws-security-token-service-multiple.md)** | - Access Key ID                                                 |
| **[JSON Web Token (JWT) Credential Providers](json-web-token-multiple.md)**      | - Username (Snowflake Server Workloads only) - HTTP header/body |

## How Credential Provider selection works

When you configure multiple Credential Providers in an Access Policy, Aembit uses selector values to determine which Credential Provider handles each request.

### AWS STS Credential Providers

AWS STS Credential Providers use **Access Key ID selectors**. Each Credential Provider in the Access Policy must have a unique Access Key ID that your application uses as a placeholder in requests. The selector reaches Aembit Cloud through whichever integration path the Client Workload uses. Agent Proxy extracts it from the AWS SigV4 Authorization header. The Aembit GitHub Action takes it as the `aws-access-key-id` input, and the Edge SDKs and Edge API send it as `connectionMetadata.accessKeyId`. Aembit Cloud routes the request to the matching Credential Provider.

For configuration procedures, see [Configure an AWS STS Federation Credential Provider](aws-security-token-service-federation.md#configure-multiple-aws-sts-credential-providers).

### JWT Credential Providers

JWT Credential Providers use **username mapping** (for Snowflake) or **HTTP header/body mapping** (for HTTP workloads). Each Credential Provider must have a unique mapping value. When a request arrives, Aembit extracts the mapping value and routes the request to the matching Credential Provider.

For configuration procedures, see [Configure a JWT Credential Provider](json-web-token.md#configure-multiple-jwt-credential-providers).

## Benefits

* **Simplified policy management** - Manage multiple credentials within a single Access Policy instead of creating separate policies for each credential scenario.
* **Scalability** - Efficiently supports multiple Credential Providers per Access Policy.
* **Seamless application experience** - Applications can access different resources with different credentials without code changes or multiple Client Workload identities.

## Related topics

* [Using multiple AWS STS Credential Providers](aws-security-token-service-multiple.md) - Learn how Aembit routes requests to multiple AWS STS Credential Providers
* [Configure an AWS STS Federation Credential Provider](aws-security-token-service-federation.md) - Configure single and multiple AWS STS Credential Providers
* [Using multiple JWT Credential Providers](json-web-token-multiple.md) - Learn how Aembit routes requests to multiple JWT Credential Providers
* [Configure a JWT Credential Provider](json-web-token.md) - Configure single and multiple JWT Credential Providers
* [Credential Providers overview](overview.md) - Overview of all available Credential Provider types
