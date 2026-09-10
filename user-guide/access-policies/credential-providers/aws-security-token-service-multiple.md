---
type: explanation
title: "Using Multiple AWS STS Credential Providers in a Single Access Policy"
description: "How to add and use multiple AWS Security Token Service (STS) Credential Providers to an Access Policy"
resource: https://docs.aembit.io/user-guide/access-policies/credential-providers/aws-security-token-service-multiple/
tags: ["credential-provider", "access-policy"]
timestamp: 2026-03-12T14:33:26-07:00
---

# Using Multiple AWS STS Credential Providers in a Single Access Policy

This page explains how Aembit enables the use of multiple [AWS Security Token Service (STS) Credential Providers](aws-security-token-service-federation.md) within a single Access Policy, allowing flexible and scalable access to AWS resources.

Unlike when using [multiple JWT-based Credential Providers](multiple-credential-providers.md) that use username or HTTP header mapping, AWS STS Credential Providers use **Access Key ID selectors** for Credential Provider matching. Each AWS STS Credential Provider that you configure in an Access Policy must have a unique **Access Key ID** that your application uses as a placeholder in requests.

> **Pre-signed URLs**
>
> Aembit doesn’t support AWS pre-signed URLs. For more information, see [Known limitations](aws-sigv4.md#known-limitations).

In complex AWS environments, applications often need to assume different IAM roles to access AWS services securely. Traditionally, this required creating separate access policies for each role, increasing operational overhead.

Aembit supports configuring multiple AWS STS Credential Providers within a single Access Policy. This enables a single Client Workload identity to seamlessly access multiple AWS resources, each with its own IAM role, by selecting the appropriate Credential Provider based on the AWS Access Key ID.

> **Edge Component minimum versions**
>
> Using multiple AWS STS Credential Providers requires the following Aembit Edge Component minimum versions:
>
> * Agent Proxy 1.27.3865
> * Agent Controller 1.27.2906

## Benefits

* **Simplified Policy Management** - Manage multiple AWS roles within a single policy, reducing configuration complexity.
* **Scalability** - Efficiently supports multiple Credential Providers (for example, 10+) per Access Policy.
* **Seamless Application Experience** - Applications can access different AWS resources without code changes or multiple workload identities.

## How it works

After you [configure multiple AWS STS Credential Providers](aws-security-token-service-federation.md#configure-multiple-aws-sts-credential-providers) in an Access Policy (each with a unique Access Key ID selector), Aembit handles requests as follows:

1. **Request interception** - When an application makes an AWS request, the Agent Proxy intercepts it and extracts the Access Key ID from the [AWS SigV4 Authorization header](aws-sigv4.md).

2. **Credential Provider matching** - The Agent Proxy sends the Access Key ID to Aembit Cloud, which matches it to the corresponding Credential Provider configured in the Access Policy.

3. **Credential issuance and injection** - Aembit Cloud assumes the IAM role via the selected Credential Provider and returns temporary AWS credentials. The Agent Proxy then injects or uses these credentials to fulfill the application’s request.

### Example scenario

Suppose your application needs to:

* Write logs to an S3 bucket (using `STS-RoleA`)
* Read data from DynamoDB (using `STS-RoleB`)

You can configure:

* `STS-RoleA`: Assumes an IAM role for S3 access, mapped to selector `AKIADUMMYFORROLEA`
* `STS-RoleB`: Assumes an IAM role for DynamoDB access, mapped to selector `AKIADUMMYFORROLEB`

Your application uses the appropriate placeholder Access Key ID to select the desired Credential Provider for each request.

### High-level workflow

The following diagram shows how the Agent Proxy routes requests through Aembit Cloud to select the appropriate Credential Provider:

![Sequence diagram showing how the Agent Proxy routes AWS requests through Aembit Cloud to select the appropriate Credential Provider based on Access Key ID](https://docs.aembit.io/d2/docs/user-guide/access-policies/credential-providers/aws-security-token-service-multiple-0.svg)

## Access authorization events

The following are example [access authorization events](../../audit-report/access-authorization-events.md) with the Event Type `access.credential` showing the use of different AWS STS Credential Providers within an Access Policy when handling requests:

Notice the differences between the two Credential Providers:

* The `serverWorkload` name reflects different AWS resources (`S3 SW` vs `DynamoDB SW`)
* The `accessPolicy` ID remains the same, indicating the same Access Policy governs both requests
* The `credentialProvider` section shows different `id` and `name` values (`STS-RoleA` vs `STS-RoleB`)

- S3 SW Credential Request

  ```json
  {
    "meta": {
      "clientIP": "18.111.222.123",
      "timestamp": "2025-11-25T11:58:58.989522Z",
      "eventType": "access.credential",
      "eventId": "521bf87e-91d8-4e9b-90c5-7a6d4d6118ce",
      "resourceSetId": "ffffffff-ffff-ffff-ffff-ffffffffffff",
      "contextId": "47fa4467-0712-4c1e-b44e-d4dbddc7844a",
      "severity": "Info"
    },
    "outcome": {
      "result": "Authorized"
    },
    "clientWorkload": {
      "id": "973fb193-828b-406e-a6be-b64db2c94fd6",
      "name": "Test Ubuntu STS CW",
      "result": "Identified"
    },
    "serverWorkload": {
      "id": "f1ebd1d-ebf4-462d-8e45-a4eeea68e480",
      "name": "S3 SW",
      "result": "Identified"
    },
    "accessPolicy": {
      "id": "da30b2f9-999a-40d2-94fe-6a0c50b837cf",
      "result": "Identified"
    },
    "trustProviders": [],
    "accessConditions": [],
    "credentialProvider": {
      "type": "aws-sts-oidc",
      "id": "b8804a83-ab97-4dc6-8bc6-2cec9f33c2b5",
      "name": "STS-RoleA",
      "result": "Retrieved"
    }
  }
  ```

- DynamoDB SW Credential Request

  ```json
  {
    "meta": {
      "clientIP": "18.111.222.123",
      "timestamp": "2025-11-25T12:05:42.123456Z",
      "eventType": "access.credential",
      "eventId": "a1b2c3d4-5678-90ab-cdef-1234567890ab",
      "resourceSetId": "ffffffff-ffff-ffff-ffff-ffffffffffff",
      "contextId": "b2c3d4e5-6789-4f1e-9abc-1234567890cd",
      "severity": "Info"
    },
    "outcome": {
      "result": "Authorized"
    },
    "clientWorkload": {
      "id": "973fb193-828b-406e-a6be-b64db2c94fd6",
      "name": "Test Ubuntu STS CW",
      "result": "Identified"
    },
    "serverWorkload": {
      "id": "f1ebd1d-ebf4-462d-8e45-a4eeea68e480",
      "name": "DynamoDB SW",
      "result": "Identified"
    },
    "accessPolicy": {
      "id": "da30b2f9-999a-40d2-94fe-6a0c50b837cf",
      "result": "Identified"
    },
    "trustProviders": [],
    "accessConditions": [],
    "credentialProvider": {
      "type": "aws-sts-oidc",
      "id": "c9905b21-1e2f-4b3c-9d7e-3f4e5a6b7c8d",
      "name": "STS-RoleB",
      "result": "Retrieved"
    }
  }
  ```

### Key fields explained

* `meta`: General metadata about the event, including client IP, timestamp, event type, and unique IDs.
* `outcome`: The result of the access attempt (for example, “Authorized”).
* `clientWorkload/serverWorkload`: Identifiers and names for the Client Workload and Server Workload involved.
* `accessPolicy`: The ID of the Access Policy that authorized the request.
* `trustProviders`: Trust Providers used to verify the Client Workload identity.
* `accessConditions`: Access Conditions evaluated for this request.
* `credentialProvider`: Details about the Credential Provider used, including its type, unique ID, and name.

## Error handling

The following rules apply when handling requests with multiple AWS STS Credential Providers:

* If the Access Key ID in a request doesn’t match any configured Credential Provider, the request fails with a `403 Forbidden` error.
* If Aembit can’t extract the Access Key ID (for example, a malformed request), credentials aren’t injected and the request fails.
* Access Key ID selector values must use uppercase characters only. Lowercase selectors won’t match.

## Related topics

* [Configure an AWS STS Federation Credential Provider](aws-security-token-service-federation.md) - Set up a single AWS STS Credential Provider
* [Configure multiple Credential Providers](multiple-credential-providers.md) - Overview of multiple Credential Provider support
* [How Aembit uses AWS SigV4 and SigV4a](aws-sigv4.md) - Learn how Aembit’s AWS STS Credential Provider works with AWS request signing
* [Credential Providers overview](overview.md) - Overview of all available Credential Provider types
* [Access Policies](../overview.md) - Learn about Aembit Access Policies and how they work
* [Access Authorization Events](../../audit-report/access-authorization-events.md) - Review access authorization event information in the Reporting Dashboard
* [AWS Cloud Server Workload](../server-workloads/guides/aws-cloud.md) - Configure Aembit to work with AWS Cloud as a Server Workload
