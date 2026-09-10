---
type: how-to
title: "Use the Edge SDK in an AWS Lambda function"
description: "Retrieve credentials from a Lambda function using the Edge SDK and the AWS Role Trust Provider"
resource: https://docs.aembit.io/dev-guide/sdk/edge/integrations/aws-lambda/
interface: sdk
tags: ["integration", "edge", "sdk"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Use the Edge SDK in an AWS Lambda function

An AWS Lambda function authenticates to Aembit through the AWS Role Trust Provider, which verifies the function’s execution role. The Edge SDK signs the request that proves the role and runs the authentication exchange. You configure the SDK client and request a credential.

## How a Lambda function proves its identity

AWS injects temporary credentials for the function’s execution role into the runtime environment. The Edge SDK uses those credentials to sign an AWS Security Token Service (STS) [GetCallerIdentity](https://docs.aws.amazon.com/STS/latest/APIReference/API_GetCallerIdentity.html) request with [AWS Signature Version 4](../../../../user-guide/access-policies/credential-providers/aws-sigv4.md), then sends the signed request data to Aembit.

Aembit calls `GetCallerIdentity` with that signed request. AWS answers with the identity behind the signature, and Aembit evaluates that identity against the match rules on your AWS Role Trust Provider. Because AWS performs the verification, the signature is what establishes the function’s identity, and the SDK never sends the role credentials themselves.

For the request and response shapes behind that exchange, see [Edge API authentication with AWS Lambda](../../../api/edge/auth/aws-lambda.md).

## Before you start

* An Aembit Tenant with at least Read-Only permission for Trust Providers.
* An Access Policy for the service your function reaches.
* An [AWS Role Trust Provider](../../../../user-guide/access-policies/trust-providers/aws-role-trust-provider.md) with match rules that identify your function’s execution role.
* A Lambda function on a Node.js 20 or later runtime, with an execution role attached.

## Get your Edge SDK Client ID

Your function needs the Edge SDK Client ID from the [AWS Role Trust Provider](../../../../user-guide/access-policies/trust-providers/aws-role-trust-provider.md) whose match rules identify your function’s execution role. Lambda execution roles match on `accountId` and `roleArn`, where the `roleArn` takes the form `arn:aws:sts::<accountId>:assumed-role/<functionRoleName>/<functionName>`.

1. Log in to your Aembit Tenant.

2. Go to the **Trust Providers** section in the left sidebar.

3. Select the Trust Provider you want to use for Edge API authentication.

4. In the **TRUST PROVIDER** section, find the **Edge SDK Client ID** field.

5. Copy the Edge SDK Client ID to use in your authentication requests.

   ![Aembit UI Trust Provider page](https://docs.aembit.io/_astro/edge-sdk-client-id.BJB7d1dG_H47oi.webp)

Your Client Workload also needs an identification method. To identify the function by its Amazon Resource Name (ARN), use [AWS Lambda ARN](../../../../user-guide/access-policies/client-workloads/identification/aws-lambda-arn.md) identification. To identify it by an explicit value instead, use Aembit Client ID identification and pass that value through the `CLIENT_WORKLOAD_ID` environment variable.

> **Two different identifiers**
>
> The SDK’s `clientId` is the Edge SDK Client ID from the Trust Provider. It isn’t the **Client Identifier** on your Client Workload, which is a separate value on a separate page that policy configuration can use. Lambda needs both when you use Aembit Client ID identification: the Edge SDK Client ID as `clientId`, and the Client Identifier as `CLIENT_WORKLOAD_ID`.

## Wire up the SDK

The example collects every value you change into one `EXAMPLE_CONFIG` object at the top of the file. The AWS Role Trust Provider also needs a region, which the example resolves from the environment rather than from `EXAMPLE_CONFIG`.

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

The function reads the rest of what it needs from environment variables:

| Variable             | Required                                  | Source                                     |
| -------------------- | ----------------------------------------- | ------------------------------------------ |
| `AWS_REGION`         | Yes                                       | Lambda sets this automatically             |
| `AWS_DEFAULT_REGION` | Only when testing outside Lambda          | You set it in your local environment       |
| `CLIENT_WORKLOAD_ID` | Only with Aembit Client ID identification | You set it on the function’s configuration |

The example throws `Missing AWS region. Set AWS_REGION or AWS_DEFAULT_REGION.` when it can resolve neither variable, so a function tested outside Lambda must set one of them itself.

> **`baseUrl` redirects**
>
> A `baseUrl` that redirects loses the `Authorization` header, so a credential request can reach Aembit unauthenticated even after authentication succeeds. Use the **Edge API Base URL** value exactly, without a trailing slash.

## Run the example

The Edge SDK repository carries a runnable version of this integration, including its build, packaging, and deployment steps.

[AWS Role Lambda example](https://github.com/Aembit/edge-sdks/tree/main/ts/examples/aws-role-lambda)Runnable TypeScript example for the AWS Role Trust Provider.

## Troubleshooting

Every error the Edge SDK raises carries a `kind`, which narrows the failure to one side of the exchange.

* **Missing AWS region**: The example resolved neither `AWS_REGION` nor `AWS_DEFAULT_REGION` and threw before contacting Aembit. Lambda normally sets `AWS_REGION`, so check this first when running the handler outside Lambda.
* **`auth`**: Aembit received the signed `GetCallerIdentity` request and rejected it. Confirm `clientId` holds the Edge SDK Client ID from the AWS Role Trust Provider. Confirm the Trust Provider’s match rules identify the function’s execution role.
* **`credential`**: Aembit authenticated the function but returned no credential for the service you requested, so `credentialType` comes back as `Unknown` with no data. Confirm that the Service Endpoint on your Server Workload exactly matches `serverHost` and `serverPort`, because Aembit resolves the Access Policy from those values. Confirm the Access Policy includes the Client Workload for this function. Confirm the Credential Provider on that policy returns the `credentialType` your function requested. When your Client Workload uses Aembit Client ID identification, confirm the function sets `CLIENT_WORKLOAD_ID`.

Alongside `kind`, each error carries `statusCode`, `apiCode`, and `requestId` when Aembit supplies them, and `retryable` to report whether another attempt might succeed. Include `requestId` when you contact Aembit Support.
