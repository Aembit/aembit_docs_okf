---
type: how-to
title: "Use the Edge SDK in an AWS Lambda function"
description: "Retrieve credentials from a Lambda function using the Edge SDK and the AWS Role Trust Provider"
resource: https://docs.aembit.io/dev-guide/sdk/edge/integrations/aws-lambda/
interface: sdk
tags: ["integration", "edge", "sdk"]
timestamp: 2026-10-15T12:57:18-07:00
---

# Use the Edge SDK in an AWS Lambda function

An AWS Lambda function authenticates to Aembit through the AWS Role Trust Provider, which verifies the function’s execution role. The Edge SDK signs the request that proves the role and runs the authentication exchange. You configure the SDK client and request a credential.

## How a Lambda function proves its identity

AWS injects temporary credentials for the function’s execution role into the runtime environment. The Edge SDK uses those credentials to sign an AWS Security Token Service (STS) [GetCallerIdentity](https://docs.aws.amazon.com/STS/latest/APIReference/API_GetCallerIdentity.html) request with [AWS Signature Version 4](../../../../user-guide/access-policies/credential-providers/aws-sigv4.md), then sends the signed request data to Aembit.

Aembit calls `GetCallerIdentity` with that signed request. AWS answers with the identity behind the signature. Aembit evaluates that identity against the match rules on your AWS Role Trust Provider. Because AWS performs the verification, the signature is what establishes the function’s identity, and the SDK never sends the role credentials themselves.

For the request and response shapes behind that exchange, see [Edge API authentication with AWS Lambda](../../../api/edge/auth/aws-lambda.md).

## Before you start

* An Aembit Tenant with at least Read Only permission for Trust Providers.
* An Access Policy for the service your function reaches.
* An [AWS Role Trust Provider](../../../../user-guide/access-policies/trust-providers/aws-role-trust-provider.md) with match rules that identify your function’s execution role.
* A Lambda function on a Node.js 20 or later runtime for TypeScript, or a Python 3.10 or later runtime for Python, with an execution role attached.

## Get your Edge SDK Client ID

Your function needs the Edge SDK Client ID from the [AWS Role Trust Provider](../../../../user-guide/access-policies/trust-providers/aws-role-trust-provider.md) whose match rules identify your function’s execution role. Lambda execution roles match on `accountId` and `roleArn`, where the `roleArn` takes the form `arn:aws:sts::<accountId>:assumed-role/<functionRoleName>/<functionName>`.

1. Log in to your Aembit Tenant.

2. Go to the **Trust Providers** section in the left sidebar.

3. Select the Trust Provider you want to use for Edge API authentication.

4. In the **TRUST PROVIDER** section, find the **Edge SDK Client ID** field.

5. Copy the Edge SDK Client ID to use in your authentication requests.

   ![Aembit UI Trust Provider page](https://docs.aembit.io/_astro/edge-sdk-client-id.BJB7d1dG_H47oi.webp)

Your Client Workload also needs an identification method. The example uses Aembit Client ID identification: it reads the Aembit Client ID value from the `CLIENT_WORKLOAD_ID` environment variable and passes it to the SDK. The SDK doesn’t send the function’s Amazon Resource Name (ARN), so [AWS Lambda ARN](../../../../user-guide/access-policies/client-workloads/identification/aws-lambda-arn.md) identification matches only when your code adds the ARN to `clientWorkloadDetails` as `aws.lambda.arn`.

> **Two different identifiers**
>
> The SDK’s `clientId` is the Edge SDK Client ID from the Trust Provider. It isn’t the **Client Identifier** on your Client Workload, which is a separate value on a separate page that policy configuration can use. Lambda needs both when you use Aembit Client ID identification: the Edge SDK Client ID as `clientId`, and the Client Identifier as `CLIENT_WORKLOAD_ID`.

## Wire up the SDK

The example collects every value you change into one `EXAMPLE_CONFIG` object at the top of the file. The AWS Role Trust Provider also needs a region, which the example resolves from the environment rather than from `EXAMPLE_CONFIG`.

* TypeScript

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

* Python

  ```python
  EXAMPLE_CONFIG = {
      "base_url": "https://<tenant>.ec.<stack>.aembit.io",
      "client_id": "your-edge-sdk-client-id",
      "server_host": "target.example.com",
      "server_port": 443,
      "credential_type": "ApiKey",
      "resource_set": None,
  }
  ```

Where each value comes from in Aembit:

| TypeScript value              | Python value                    | Where it comes from                                                                                                                                                                                    |
| ----------------------------- | ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `baseUrl`                     | `base_url`                      | The Edge API base URL in your Aembit Tenant profile. See [Get your Aembit Edge API base URL](../../../api/edge/quickstart-edge.md#get-your-aembit-edge-api-base-url).                                   |
| `clientId`                    | `client_id`                     | The Edge SDK Client ID you copied from the Trust Provider.                                                                                                                                             |
| `serverHost` and `serverPort` | `server_host` and `server_port` | The **Host** and **Port** under **Service Endpoint** on your Server Workload, which Aembit uses to find the Access Policy. **Port** must be equal, and **Host** must be equal or match a `*` wildcard. |
| `credentialType`              | `credential_type`               | The credential type your Credential Provider returns.                                                                                                                                                  |
| `resourceSet`                 | `resource_set`                  | Leave unset unless your Access Policy lives in a non-default Resource Set. It takes the Resource Set ID, not its name.                                                                                 |

The function reads the rest of what it needs from environment variables:

| Variable             | Required                                  | Source                                     |
| -------------------- | ----------------------------------------- | ------------------------------------------ |
| `AWS_REGION`         | Yes                                       | Lambda sets this automatically             |
| `AWS_DEFAULT_REGION` | Only when testing outside Lambda          | You set it in your local environment       |
| `CLIENT_WORKLOAD_ID` | Only with Aembit Client ID identification | You set it on the function’s configuration |

The example stops with `Missing AWS region. Set AWS_REGION or AWS_DEFAULT_REGION.` when it can resolve neither variable, so a function tested outside Lambda must set one of them itself.

> **`baseUrl` redirects**
>
> A `baseUrl` that redirects can break Edge SDK requests. A `301`, `302`, or `303` redirect turns each request into a `GET` without its body. In TypeScript, a `307` or `308` redirect to another host lets authentication succeed, then drops the `Authorization` header from the credential request, so Aembit returns `401`. In Python, a `307` or `308` redirect fails with that status code. Use the Edge API base URL from your profile exactly, without a trailing slash.

## Run the example

The Edge SDK repository carries a runnable TypeScript version of this integration, including its build, packaging, and deployment steps. The Python example runs the same AWS Role flow as a script wherever AWS credentials and a region are available. To deploy it as a function, call its logic from a Lambda handler.

[AWS Role Lambda example (TypeScript)](https://github.com/Aembit/edge-sdks/tree/main/ts/examples/aws-role-lambda)Runnable TypeScript example for the AWS Role Trust Provider.

[AWS Role example (Python)](https://github.com/Aembit/edge-sdks/tree/main/py/examples/aws_role)Runnable Python script for the AWS Role Trust Provider.

## Troubleshooting

Every error the Edge SDK raises carries a `kind`, which narrows the failure to one side of the exchange. Alongside `kind`, each error carries `statusCode`, `apiCode`, and `requestId` when Aembit supplies them, and `retryable` to report whether another attempt might succeed. The Python SDK names these fields `status_code`, `api_code`, `request_id`, and `retryable`. Include `requestId` when you contact Aembit Support.

### The example reports a missing AWS region

The example resolved neither `AWS_REGION` nor `AWS_DEFAULT_REGION` and threw before contacting Aembit. Lambda normally sets `AWS_REGION`, so when you run the handler outside Lambda, set `AWS_REGION` yourself.

### Authentication fails

The SDK raises an error with the `auth` kind when Aembit rejects the authentication request. Aembit records no access authorization event for an authentication request, so the status code and these checks are the evidence you have.

A `400` status means Aembit couldn’t read the Client ID or the Resource Set ID in the request. Copy the **Edge SDK Client ID** from the Trust Provider again, and confirm `resourceSet` holds a Resource Set ID as the third check in the following list describes.

A `401` status carries the same message whichever check failed. Work through the checks in order:

1. Confirm `clientId` (Python `client_id`) holds the **Edge SDK Client ID** of the Trust Provider you set up for this workload. The value ends with that Trust Provider’s **ID**, so compare the last segment with the **ID** field in the Trust Provider editor. If they differ, copy the **Edge SDK Client ID** from the right Trust Provider.

2. Confirm the Trust Provider’s toggle reads **Active**. If it reads **Inactive**, switch it on in the Trust Provider editor. The change saves at once, after a confirmation dialog when the Trust Provider is in use.

3. Confirm the Trust Provider belongs to the Resource Set the request names. `resourceSet` (Python `resource_set`) takes the Resource Set ID, not its name. When you leave it unset, the Trust Provider must belong to the **Default** Resource Set. To find the ID, select **Administration ☰ Resource Sets** and hover over the Resource Set name, or open the Resource Set and copy **Resource Set ID**. To see which Resource Set holds the Trust Provider, select each Resource Set in the selector at the top of the **Trust Providers** page.

   > **Not enabled by default**
   >
   > Contact [Aembit support](https://aembit.io/contact) to enable this feature.

4. Confirm the identity evidence passes the Trust Provider’s validation, using the checks after this list where your Trust Provider type has them. Aembit evaluates match rules only after the token or document passes validation, so a validation failure looks like a match rule failure.

5. Confirm the Trust Provider’s match rules hold this workload’s values. Aembit groups the rules by **Attribute**, and by **Key** for custom claim rules, and each group needs a matching rule. A **Value** containing `*` matches any run of characters in that position. Any other value must match exactly, including case, unless your Trust Provider’s checks after this list say otherwise. A rule with an empty **Value** fails the Trust Provider, so fill in the **Value** or delete the rule. A Trust Provider with no match rules accepts any workload whose evidence passes validation. Compare each rule’s **Value** with the matching field in real evidence from this workload, and correct the rule.

For an AWS Role Trust Provider, Aembit sends the signed AWS Security Token Service (STS) `GetCallerIdentity` request on to AWS and compares the identity AWS returns with the match rules. `accountId` compares `Account`, `roleArn` compares `Arn`, `assumedRole` compares the role name, the part of `Arn` between the first and second `/`, and `userId` compares `UserId`. For a Lambda function, `Arn` takes the `assumed-role` form shown earlier on this page, so a `roleArn` rule that holds the IAM role’s own ARN doesn’t match.

### The credential request raises an error

The SDK raises an error with the `credential` kind when the credential request returns an error status. The error’s message holds the message from Aembit’s response. When the response carries none, the message reads `Edge API request failed with status` followed by the status code.

An error with no `statusCode` (Python `status_code`) whose message names `server.host` or `server.port` comes from the SDK, which checks those values before it contacts Aembit. Set `server.host` to a non-empty string and `server.port` to a whole number from 1 to 65535.

**A `404` status.** Aembit identified the Client Workload and the Server Workload, then couldn’t settle on one Access Policy or one Credential Provider. The response carries no message, so check each cause:

* No active Access Policy holds this Server Workload and a Client Workload that matches this workload, or more than one does. More than one Client Workload can match one workload. Count the active Access Policies across every matching Client Workload, and keep exactly one. A new Access Policy saved with **Save Policy** stays inactive until you switch on its toggle, while **Save Policy & Activate** saves it as active.
* The Access Policy holds Credential Providers of different types. Keep Credential Providers of one type on the Access Policy.
* `connectionMetadata` (Python `connection_metadata`) selected none of the Access Policy’s Credential Providers, or more than one. Aembit chooses by `connectionMetadata` when it carries a filled value: `accountName`, `accessKeyId`, both `headerName` and `headerValue`, or both `httpBodyFieldPath` and `httpBodyFieldValue`. It chooses this way even when the Access Policy holds one Credential Provider. Leave it unset when the Access Policy holds one Credential Provider, and otherwise set it to values that select exactly one.
* The Credential Provider the request selected is inactive. Switch its toggle to **Active**. Aembit records no access authorization event for this cause.

**A `400` status.** When the message is Aembit’s own, Aembit failed while it selected or fetched the credential, and the message says why. Examples include an error status from the service the Credential Provider calls, and a key missing from a secret the Credential Provider reads. Correct the Credential Provider or the service it calls, then retry. An Access Policy with more than one Credential Provider also returns `400` when `connectionMetadata` carries no filled value, or the request carries no `connectionMetadata`. An AWS Secrets Manager Value or Azure Key Vault Value Credential Provider with **Private Network Access** switched on returns `400` for every credential type the SDK requests. Switch **Private Network Access** off on that Credential Provider to retrieve its secret through the Edge SDK. A `400` whose message reads `Edge API request failed with status 400` means Aembit rejected the request before it looked for a credential. Confirm `credentialType` (Python `credential_type`) is a valid credential type, such as `ApiKey` or `OAuthToken`. When you set `certSigningRequest` (Python `cert_signing_request`), confirm it’s valid base64. When you set `resourceSet` (Python `resource_set`), confirm it’s the ID of a Resource Set in your Aembit Tenant.

**A `401` status after authentication succeeds.** Confirm `baseUrl` (Python `base_url`) is the Edge API base URL from your Aembit Tenant profile, exactly. In TypeScript, a `307` or `308` redirect to another host drops the `Authorization` header from the credential request.

### The credential comes back empty

Aembit returns status `200` for these responses, so the SDK raises no error, and the field your code reads in the credential’s `data` is empty. The credential’s `credentialType` (Python `credential_type`) tells the two causes apart.

**`credentialType` matches the type you requested.** Aembit doesn’t compare the requested type with the Credential Provider’s type. The requested type picks which fields of `data` Aembit fills, and the response repeats it. When the two types differ, the credential can arrive in a field your code doesn’t read or come back with no value. Some Credential Providers raise a `400` credential error instead. When the Server Workload uses **Authentication Method** HTTP Authentication with **Authentication Scheme** Basic, a Username & Password Credential Provider returns the base64-encoded `username:password` value in both `username` and `password`. Request the type that matches the Credential Provider on the Access Policy:

| Credential Provider                                    | `credentialType`   | Fields in `data`                                          |
| ------------------------------------------------------ | ------------------ | --------------------------------------------------------- |
| API Key                                                | `ApiKey`           | `apiKey`                                                  |
| Username & Password                                    | `UsernamePassword` | `username`, `password`                                    |
| AWS Secrets Manager Value (username and password)      | `UsernamePassword` | `username`, `password`                                    |
| AWS Secrets Manager Value (plain text or single value) | None               | None, because the Edge API can’t return this secret       |
| Azure Key Vault Value (username and password)          | `UsernamePassword` | `username`, `password`                                    |
| AWS Security Token Service Federation                  | `AwsStsFederation` | `awsAccessKeyId`, `awsSecretAccessKey`, `awsSessionToken` |
| X.509-SVID Certificate                                 | `X509Svid`         | `certificate`                                             |
| Any other type except AWS Secrets Manager Value        | `OAuthToken`       | `token`                                                   |

A request with no `credentialType` gets `OAuthToken`. An `X509Svid` request also needs `certSigningRequest` (Python `cert_signing_request`).

**`credentialType` is `Unknown`.** Aembit authenticated the workload but returned no credential. Find the request’s event in **Reporting**, as [Retrieving Access Authorization Event data](../../../../user-guide/audit-report/access-authorization-events.md#retrieving-access-authorization-event-data) describes. The event records a `result` for the Client Workload, the Server Workload, the Access Policy, each Trust Provider, and each Access Condition, and `NotFound` marks one Aembit couldn’t identify. Fix the first one that didn’t succeed:

* **Server Workload**: Aembit compares `server.host` and `server.port` with **Host** and **Port** under **Service Endpoint**, not with **Forward to Port**. **Host** matches when it equals `server.host`, and a `*` in **Host** matches any run of characters. Aembit doesn’t compare **Transport Protocol** or the **TLS** settings. The Server Workload must also be **Active** and belong to an active Access Policy.
* **Client Workload**: each identifier type under **Client Identification** must match this workload. Rows of the same type are alternatives, except **OIDC ID Token** rows with different **Claim Name** values, which must each match. The Client Workload must also be **Active** and belong to an active Access Policy. The identifier types that fit this platform appear at the end of this section.
* **Trust Provider** with the result `Unauthorized`: Aembit checks the workload’s evidence again on every credential request. Every active Trust Provider on the Access Policy must attest the workload, and Aembit stops at the first that fails. A Trust Provider this workload can’t satisfy, such as one for another platform, fails every request. Switch off its **Active** toggle, remove it from the Access Policy, or give the workload its own Access Policy. The event’s `reason` names the check that failed. For `MatchRuleFailed`, the event also records the `attribute`, `expectedValue` or `expectedValues`, and `actualValue`, so correct that match rule’s **Value**. When the Trust Provider validates an identity token and the event records no `actualValue`, Aembit couldn’t compare the rule. Give each match rule a **Value**, and confirm the token carries each claim the match rules name. For any other `reason`, work through the Trust Provider checks in the Authentication fails section of this page.
* **Access Condition** with the result `Unauthorized`: meet the condition, or remove it from the Access Policy.
* **Every entity succeeded**: confirm the Access Policy has a Credential Provider. When the event’s `credentialProvider` entry carries a `reason`, [Credential failure](../../../../user-guide/audit-report/access-authorization-events.md#credential-failure) lists what each reason means.

When a Client Workload uses the **Aembit Client ID** identifier, the SDK doesn’t read the `CLIENT_WORKLOAD_ID` environment variable. Pass the value in your client configuration instead, as `clientWorkloadDetails: { os: { environment: { CLIENT_WORKLOAD_ID: "<value>" } } }`, or as the same structure in `client_workload_details` in Python.

For this function, **Aembit Client ID** matches the value the example passes from `CLIENT_WORKLOAD_ID`. **AWS Lambda Arn** matches only when your code adds the function’s ARN to `clientWorkloadDetails` as `aws.lambda.arn`, because the SDK doesn’t send it.
