---
type: how-to
title: "Use the Edge SDK on an AWS EC2 instance"
description: "Retrieve credentials from an EC2 instance using the Edge SDK and the AWS Metadata Service Trust Provider"
resource: https://docs.aembit.io/dev-guide/sdk/edge/integrations/aws-ec2/
interface: sdk
tags: ["integration", "edge", "sdk"]
timestamp: 2026-09-22T13:47:38-07:00
---

# Use the Edge SDK on an AWS EC2 instance

An application on an AWS EC2 instance authenticates to Aembit through the AWS Metadata Service Trust Provider for that instance. That Trust Provider verifies the signed instance identity document that the AWS Instance Metadata Service (IMDS) provides. The Edge SDK reads that document and runs the authentication exchange. You configure the SDK client and request a credential.

## How an EC2 instance proves its identity

An EC2 instance reads a signed instance identity document from IMDS over a link-local HTTP request. The document describes the instance, including its account, region, and instance ID. AWS signs the document, so the signature proves it came from AWS rather than from the workload.

The Edge SDK reads both the document and its signature over IMDSv2 and sends them to Aembit. Aembit validates the signature. It then evaluates the document’s fields against the match rules on your AWS Metadata Service Trust Provider. Because the signed document is the evidence, no credential or token has to exist on the instance ahead of time.

For the request and response shapes behind that exchange, see [Edge API authentication with AWS Metadata Service](../../../api/edge/auth/aws-metadata-service.md).

## Before you start

* An Aembit Tenant with at least Read Only permission for Trust Providers.
* An Access Policy for the service your application reaches.
* An [AWS Metadata Service Trust Provider](../../../../user-guide/access-policies/trust-providers/aws-metadata-service-trust-provider.md) with match rules that identify your EC2 instance.
* An EC2 instance with IMDSv2 enabled and reachable at `169.254.169.254`, running Node.js 20 or later.

## Get your Edge SDK Client ID

Your application needs the Edge SDK Client ID from the [AWS Metadata Service Trust Provider](../../../../user-guide/access-policies/trust-providers/aws-metadata-service-trust-provider.md) whose match rules identify your EC2 instance.

1. Log in to your Aembit Tenant.

2. Go to the **Trust Providers** section in the left sidebar.

3. Select the Trust Provider you want to use for Edge API authentication.

4. In the **TRUST PROVIDER** section, find the **Edge SDK Client ID** field.

5. Copy the Edge SDK Client ID to use in your authentication requests.

   ![Aembit UI Trust Provider page](https://docs.aembit.io/_astro/edge-sdk-client-id.BJB7d1dG_H47oi.webp)

> **Two different identifiers**
>
> The SDK’s `clientId` is the Edge SDK Client ID from the Trust Provider. It isn’t the **Client Identifier** on your Client Workload, which is a separate value on a separate page that policy configuration can use.

## Wire up the SDK

The example collects every value you change into one `EXAMPLE_CONFIG` object at the top of the file. The AWS Metadata Service Trust Provider takes no configuration of its own, because the SDK reads what it needs from IMDS at runtime.

```typescript
const EXAMPLE_CONFIG = {
  baseUrl: "https://<tenant>.ec.<stack>.aembit.io",
  clientId: "your-edge-sdk-client-id",
  serverHost: "target.example.com",
  serverPort: 443,
  credentialType: "ApiKey",
  resourceSet: undefined as string | undefined
}
```

Where each value comes from in Aembit:

| Value                         | Where it comes from                                                                                                                                                            |
| ----------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `baseUrl`                     | The **Edge API Base URL** field in your Aembit Tenant profile. See [Get your Aembit Edge API base URL](../../../api/edge/quickstart-edge.md#get-your-aembit-edge-api-base-url). |
| `clientId`                    | The Edge SDK Client ID you copied from the Trust Provider.                                                                                                                     |
| `serverHost` and `serverPort` | The Service Endpoint on your Server Workload, matched exactly, because Aembit resolves the Access Policy from these values.                                                    |
| `credentialType`              | The credential type your Credential Provider returns.                                                                                                                          |
| `resourceSet`                 | Leave undefined unless your Access Policy lives in a non-default Resource Set.                                                                                                 |

> **`baseUrl` redirects**
>
> A `baseUrl` that redirects loses the `Authorization` header, so a credential request can reach Aembit unauthenticated even after authentication succeeds. Use the **Edge API Base URL** value exactly, without a trailing slash.

## Run the example

The Edge SDK repository carries a runnable version of this integration, including how to bundle it and copy it to an instance.

[AWS IMDS EC2 example](https://github.com/Aembit/edge-sdks/tree/main/ts/examples/aws-imds-ec2)Runnable TypeScript example for the AWS Metadata Service Trust Provider.

## Troubleshooting

Every error the Edge SDK raises carries a `kind`, which narrows the failure to one side of the exchange.

* **`auth`**: Aembit received the instance identity document and rejected it. Confirm `clientId` holds the Edge SDK Client ID from the AWS Metadata Service Trust Provider. Confirm the Trust Provider’s match rules identify this instance’s account, region, and instance ID.
* **`credential`**: Aembit authenticated the instance but returned no credential for the service you requested. Confirm that the Service Endpoint on your Server Workload exactly matches `serverHost` and `serverPort`, because Aembit resolves the Access Policy from those values. Confirm the Access Policy includes the Client Workload for this instance. Confirm the Credential Provider on that policy returns the `credentialType` your application requested.

Alongside `kind`, each error carries `statusCode`, `apiCode`, and `requestId` when Aembit supplies them, and `retryable` to report whether another attempt might succeed. Include `requestId` when you contact Aembit Support.
