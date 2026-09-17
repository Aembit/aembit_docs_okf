---
type: explanation
title: "Using Multiple AWS STS Credential Providers in a Single Access Policy"
description: "How to add and use multiple AWS Security Token Service (STS) Credential Providers to an Access Policy"
resource: https://docs.aembit.io/user-guide/access-policies/credential-providers/aws-security-token-service-multiple/
tags: ["credential-provider", "access-policy"]
timestamp: 2026-09-16T18:21:40-07:00
---

# Using Multiple AWS STS Credential Providers in a Single Access Policy

This page explains how Aembit routes requests to multiple [AWS Security Token Service (STS) Credential Providers](aws-security-token-service-federation.md) within a single Access Policy.

Unlike when using [multiple JWT-based Credential Providers](multiple-credential-providers.md) that use username or HTTP header mapping, AWS STS Credential Providers use **Access Key ID selectors** for Credential Provider matching. Each AWS STS Credential Provider that you configure in an Access Policy must have a unique **Access Key ID** that your application uses as a placeholder in requests. How that selector reaches Aembit depends on the integration path. Agent Proxy reads it from the intercepted request. The Aembit GitHub Action, the Edge SDKs, and the Edge API send it as a parameter of the credential request.

> **Pre-signed URLs**
>
> Aembit doesn’t support AWS pre-signed URLs. For more information, see [Known limitations](aws-sigv4.md#known-limitations).

In complex AWS environments, applications often need to assume different IAM roles to access AWS services securely. Traditionally, this required creating separate access policies for each role, increasing operational overhead.

You can add multiple AWS STS Credential Providers to one Access Policy. This enables a single Client Workload identity to seamlessly access multiple AWS resources, each with its own IAM role, by selecting the appropriate Credential Provider based on the AWS Access Key ID.

> **Edge Component minimum versions**
>
> Using multiple AWS STS Credential Providers through Agent Proxy requires the following Aembit Edge Component minimum versions:
>
> * Agent Proxy 1.27.3865
> * Agent Controller 1.27.2906
>
> The GitHub Action, Edge SDK, and Edge API paths don’t use these components.

## Benefits

* **Simplified Policy Management** - Manage multiple AWS roles within a single policy, reducing configuration complexity.
* **Scalability** - Efficiently supports multiple Credential Providers (for example, 10+) per Access Policy.
* **Seamless Application Experience** - Applications can access different AWS resources without code changes or multiple workload identities.

## How it works

After you [configure multiple AWS STS Credential Providers](aws-security-token-service-federation.md#configure-multiple-aws-sts-credential-providers) in an Access Policy (each with a unique Access Key ID selector), Aembit handles requests as follows:

1. **Selector delivery** - The Client Workload’s integration path delivers the Access Key ID selector to Aembit Cloud as part of the credential request. See [Selector paths](#selector-paths) for where each path takes the selector from.

2. **Credential Provider matching** - Aembit Cloud matches the Access Key ID to the corresponding Credential Provider configured in the Access Policy.

3. **Credential issuance** - Aembit Cloud assumes the IAM role via the selected Credential Provider and returns temporary AWS credentials. Agent Proxy injects them into the application’s request. The GitHub Action and the Edge SDKs hand them to your workflow or code directly.

### Selector paths

| Integration path     | Where the selector comes from                                      |
| -------------------- | ------------------------------------------------------------------ |
| Agent Proxy          | The Access Key ID in the AWS SigV4 Authorization header            |
| Aembit GitHub Action | The `aws-access-key-id` input on the `Aembit/get-credentials` step |
| Edge API             | The `connectionMetadata.accessKeyId` field in the request body     |
| Edge SDK             | The `accessKeyId` connection metadata on the credential request    |

Behind Agent Proxy, your application sets the selector as its AWS Access Key ID and needs no other change. Agent Proxy extracts it from the [AWS SigV4 Authorization header](aws-sigv4.md). Without Agent Proxy, your workflow or code names the selector when it requests the credential. For each path, see:

* [Retrieve credentials with the Aembit GitHub Action](../../deploy-install/ci-cd/github/github-actions-how-to.md#configure-the-action)
* [Edge API: Connection metadata](../../../dev-guide/api/edge/endpoints/credentials.md#connection-metadata)
* [Edge SDK: Select among multiple Credential Providers](../../../dev-guide/sdk/edge/multiple-credential-providers.md)

The same Access Policy serves every path, so a workload behind Agent Proxy and a GitHub workflow can select the same Credential Providers with the same selector values.

### Example scenario

Suppose your application needs to:

* Write logs to an S3 bucket (using `STS-RoleA`)
* Read data from DynamoDB (using `STS-RoleB`)

You can configure:

* `STS-RoleA`: Assumes an IAM role for S3 access, mapped to selector `AKIADUMMYFORROLEA`
* `STS-RoleB`: Assumes an IAM role for DynamoDB access, mapped to selector `AKIADUMMYFORROLEB`

Your application uses the appropriate placeholder Access Key ID to select the desired Credential Provider for each request.

### High-level workflow

The following diagram shows the Agent Proxy path. Agent Proxy extracts the selector from the application’s request, and Aembit Cloud selects the matching Credential Provider:

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

* Behind Agent Proxy, an Access Key ID that matches no configured Credential Provider fails the request with a `403 Forbidden` error.
* On the Edge API path, an Access Key ID that matches no configured Credential Provider returns `404 Not Found` with `credentialType` set to `Unknown`.
* On the Edge API path, a request that sends no Access Key ID at all returns `400 Bad Request` when the Access Policy holds more than one Credential Provider.
* If Agent Proxy can’t extract the Access Key ID (for example, a malformed request), credentials aren’t injected and the request fails.
* Access Key ID selector values must use uppercase characters only. Lowercase selectors won’t match.

## Related topics

* [Configure an AWS STS Federation Credential Provider](aws-security-token-service-federation.md) - Set up a single AWS STS Credential Provider
* [Configure multiple Credential Providers](multiple-credential-providers.md) - Overview of multiple Credential Provider support
* [How Aembit uses AWS SigV4 and SigV4a](aws-sigv4.md) - Learn how Aembit’s AWS STS Credential Provider works with AWS request signing
* [Credential Providers overview](overview.md) - Overview of all available Credential Provider types
* [Access Policies](../overview.md) - Learn about Aembit Access Policies and how they work
* [Access Authorization Events](../../audit-report/access-authorization-events.md) - Review access authorization event information in the Reporting Dashboard
* [AWS Cloud Server Workload](../server-workloads/guides/aws-cloud.md) - Configure Aembit to work with AWS Cloud as a Server Workload
