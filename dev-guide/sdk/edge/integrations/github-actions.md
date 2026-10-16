---
type: how-to
title: "Use the Edge SDK in GitHub Actions"
description: "Authenticate a GitHub Actions workflow with its OIDC ID token and retrieve a credential through the Aembit Edge SDK."
resource: https://docs.aembit.io/dev-guide/sdk/edge/integrations/github-actions/
interface: sdk
tags: ["integration", "edge", "sdk"]
timestamp: 2026-10-15T12:57:18-07:00
---

# Use the Edge SDK in GitHub Actions

A GitHub Actions job can ask GitHub for a signed OpenID Connect (OIDC) ID token that describes the workflow run and its repository. The Edge SDK presents that token to a GitHub Action ID Token Trust Provider in Aembit, so a program in the workflow needs no stored secret to authenticate with Aembit.

This guide covers the parts specific to GitHub Actions. For what the SDK does in general, see the [Edge SDK overview](../overview.md).

If a workflow step needs credentials as step outputs rather than in your own program, the [Aembit GitHub Action](../../../../user-guide/deploy-install/ci-cd/github/overview.md) retrieves them without code. Use the Edge SDK when a TypeScript or Python program in the workflow requests the credential itself.

## Before you start

On the GitHub side, you need a repository with a workflow you can edit. The Python example needs Python 3.10 or later.

In Aembit, configure an Access Policy with these pieces:

* A [GitHub Action ID Token Trust Provider](../../../../user-guide/access-policies/trust-providers/github-trust-provider.md) carrying an Edge SDK Client ID.
* A Client Workload whose identifier matches the workflow, such as [GitHub ID Token Repository](../../../../user-guide/access-policies/client-workloads/identification/github-id-token-repository.md) or [GitHub ID Token Subject](../../../../user-guide/access-policies/client-workloads/identification/github-id-token-subject.md).
* A Server Workload whose Service Endpoint host and port match what your program requests.
* A Credential Provider that returns the credential type your program asks for.

## Where the token comes from

GitHub issues the token only to a job that has the `id-token: write` permission. Add this block to the workflow or to the job that runs your program:

**.github/workflows/aembit.yml**

```yaml
permissions:
  id-token: write
  contents: read
```

With that permission, GitHub sets two environment variables in the job: `ACTIONS_ID_TOKEN_REQUEST_URL` and `ACTIONS_ID_TOKEN_REQUEST_TOKEN`. The program requests the token from that URL, adds an `audience` query parameter, and sends the request token as a bearer token in the `Authorization` header. GitHub returns JSON whose `value` field holds the ID token.

Set the audience to the **Edge SDK Audience** value shown on your GitHub Action ID Token Trust Provider, which takes the form `https://<tenant>.id.<stack>.aembit.io`. Aembit rejects a token whose audience doesn’t match. The audience host uses the `id` subdomain, and `baseUrl` uses the `ec` subdomain.

## Write the program

The program fetches the token once, before it builds the client, and passes it to the Trust Provider as a string. For local testing outside GitHub Actions, it uses the `GITHUB_IDENTITY_TOKEN` environment variable when you set it. The SDK rejects an empty token with the `trust_provider` error kind before it contacts Aembit.

* TypeScript

  ```typescript
  import { EdgeClient, TrustProviderError, trustProviders } from "@aembit/edge-sdk"


  const EXAMPLE_CONFIG = {
    baseUrl: "https://<tenant>.ec.<stack>.aembit.io",
    clientId: "your-edge-sdk-client-id",
    aembitIdentityAudience: "https://<tenant>.id.<stack>.aembit.io",
    serverHost: "target.example.com",
    serverPort: 443,
    credentialType: "ApiKey",
    resourceSet: undefined as string | undefined
  }


  async function resolveGitHubIdentityToken(): Promise<string> {
    const envToken = process.env.GITHUB_IDENTITY_TOKEN?.trim()
    if (envToken) {
      return envToken
    }


    const requestToken = process.env.ACTIONS_ID_TOKEN_REQUEST_TOKEN
    const requestUrl = process.env.ACTIONS_ID_TOKEN_REQUEST_URL
    if (!requestToken || !requestUrl) {
      throw new TrustProviderError(
        "GitHub OIDC token could not be resolved. In GitHub Actions, set permissions: id-token: write.",
        { retryable: false }
      )
    }


    const audience = encodeURIComponent(EXAMPLE_CONFIG.aembitIdentityAudience)
    const response = await fetch(`${requestUrl}&audience=${audience}`, {
      headers: { Authorization: `bearer ${requestToken}` },
      signal: AbortSignal.timeout(5000)
    })
    if (!response.ok) {
      throw new TrustProviderError(`GitHub Actions token request returned ${response.status}`, {
        retryable: true
      })
    }


    const body = (await response.json()) as { value?: string }
    return body.value?.trim() ?? ""
  }


  async function main() {
    const trustProvider = trustProviders.githubIdentityToken({
      identityToken: await resolveGitHubIdentityToken()
    })


    const client = new EdgeClient({
      baseUrl: EXAMPLE_CONFIG.baseUrl,
      clientId: EXAMPLE_CONFIG.clientId,
      trustProvider,
      resourceSet: EXAMPLE_CONFIG.resourceSet
    })


    const credential = await client.getCredential({
      server: { host: EXAMPLE_CONFIG.serverHost, port: EXAMPLE_CONFIG.serverPort },
      credentialType: EXAMPLE_CONFIG.credentialType
    })


    console.log(
      JSON.stringify(
        {
          credentialType: credential.credentialType ?? null,
          expiresAt: credential.expiresAt ?? null,
          dataKeys: Object.keys(credential.data)
        },
        null,
        2
      )
    )
  }


  main().catch((error: unknown) => {
    console.error(error)
    process.exitCode = 1
  })
  ```

* Python

  ```python
  import json
  import os
  import urllib.request
  from typing import Any


  from aembit_edge import CredentialServerRef, EdgeClient, EdgeClientConfig, GetCredentialInput
  from aembit_edge.errors import TrustProviderError
  from aembit_edge.trust_providers import GitHubTrustProvider


  EXAMPLE_CONFIG: dict[str, Any] = {
      "base_url": "https://<tenant>.ec.<stack>.aembit.io",
      "client_id": "your-edge-sdk-client-id",
      "aembit_identity_audience": "https://<tenant>.id.<stack>.aembit.io",
      "server_host": "target.example.com",
      "server_port": 443,
      "credential_type": "ApiKey",
      "resource_set": None,
  }




  def resolve_github_identity_token() -> str:
      env_token = os.environ.get("GITHUB_IDENTITY_TOKEN", "").strip()
      if env_token:
          return env_token


      request_token = os.environ.get("ACTIONS_ID_TOKEN_REQUEST_TOKEN")
      request_url = os.environ.get("ACTIONS_ID_TOKEN_REQUEST_URL")
      if not request_token or not request_url:
          raise TrustProviderError(
              "GitHub OIDC token could not be resolved. "
              "In GitHub Actions, set permissions: id-token: write.",
              retryable=False,
          )


      audience = EXAMPLE_CONFIG["aembit_identity_audience"]
      req = urllib.request.Request(
          f"{request_url}&audience={audience}",
          headers={"Authorization": f"bearer {request_token}"},
      )
      with urllib.request.urlopen(req, timeout=5.0) as response:
          res_data = json.loads(response.read().decode("utf-8"))
      identity_token: str = res_data.get("value", "").strip()
      return identity_token




  def main() -> None:
      trust_provider = GitHubTrustProvider(identity_token=resolve_github_identity_token())


      client = EdgeClient(
          EdgeClientConfig(
              base_url=EXAMPLE_CONFIG["base_url"],
              client_id=EXAMPLE_CONFIG["client_id"],
              trust_provider=trust_provider,
              resource_set=EXAMPLE_CONFIG["resource_set"],
          )
      )


      credential = client.get_credential(
          GetCredentialInput(
              server=CredentialServerRef(
                  host=EXAMPLE_CONFIG["server_host"],
                  port=EXAMPLE_CONFIG["server_port"],
              ),
              credential_type=EXAMPLE_CONFIG["credential_type"],
          )
      )


      print(f"Payload Keys: {list(credential.data.keys())}")




  if __name__ == "__main__":
      main()
  ```

The [runnable Python example in the SDK repository](https://github.com/Aembit/edge-sdks/tree/main/py/examples/github_actions) adds the error handling this excerpt trims, along with a sample workflow.

## Fill in your values

Set each value in `EXAMPLE_CONFIG` at the top of the program:

| TypeScript value              | Python value                    | Where it comes from                                                                                                                                                                                    |
| ----------------------------- | ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `baseUrl`                     | `base_url`                      | The Edge API base URL in your Aembit Tenant profile. See [Get your Aembit Edge API base URL](../../../api/edge/quickstart-edge.md#get-your-aembit-edge-api-base-url).                                   |
| `clientId`                    | `client_id`                     | The Edge SDK Client ID from your GitHub Action ID Token Trust Provider. See [How to find your Edge SDK Client ID](#how-to-find-your-edge-sdk-client-id).                                               |
| `aembitIdentityAudience`      | `aembit_identity_audience`      | The **Edge SDK Audience** on the same Trust Provider, in the form `https://<tenant>.id.<stack>.aembit.io`.                                                                                             |
| `serverHost` and `serverPort` | `server_host` and `server_port` | The **Host** and **Port** under **Service Endpoint** on your Server Workload, which Aembit uses to find the Access Policy. **Port** must be equal, and **Host** must be equal or match a `*` wildcard. |
| `credentialType`              | `credential_type`               | The credential type your Credential Provider returns, such as `ApiKey`.                                                                                                                                |
| `resourceSet`                 | `resource_set`                  | Leave unset unless your Access Policy lives in a non-default Resource Set. It takes the Resource Set ID, not its name.                                                                                 |

Use the Edge API base URL from your profile exactly, without a trailing slash. A `baseUrl` that redirects can break Edge SDK requests. A `301`, `302`, or `303` redirect turns each request into a `GET` without its body. In TypeScript, a `307` or `308` redirect to another host lets authentication succeed, then drops the `Authorization` header from the credential request, so Aembit returns `401`. In Python, a `307` or `308` redirect fails with that status code.

## Check the result

Run the program in a job with the `id-token: write` permission, and read the job log. A working flow reports the keys in the credential payload without printing the secret.

* TypeScript

  ```json
  {
    "credentialType": "ApiKey",
    "expiresAt": "2026-03-10T19:19:09.2559713Z",
    "dataKeys": ["apiKey"]
  }
  ```

* Python

  ```text
  Payload Keys: ['apiKey']
  ```

## Troubleshooting

Every error the Edge SDK raises carries a `kind`, which narrows the failure to one side of the exchange. Alongside `kind`, each error carries `statusCode`, `apiCode`, and `requestId` when Aembit supplies them, and `retryable` to report whether another attempt might succeed. The Python SDK names these fields `status_code`, `api_code`, `request_id`, and `retryable`.

### The program reports that it couldn’t resolve the GitHub token

The program raises this error before it contacts Aembit, because `ACTIONS_ID_TOKEN_REQUEST_URL` or `ACTIONS_ID_TOKEN_REQUEST_TOKEN` is missing. Confirm the workflow or job grants `id-token: write`. Locally, set `GITHUB_IDENTITY_TOKEN` instead.

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

For a GitHub Action ID Token Trust Provider, the validation in the fourth check includes these checks:

* The token’s audience must equal the **Edge SDK Audience** on the Trust Provider. If it differs, set `aembitIdentityAudience` (Python `aembit_identity_audience`) to that value.
* The token’s `iss` claim must equal the issuer Aembit read from the **OIDC Endpoint** when you saved the Trust Provider, which is `https://token.actions.githubusercontent.com` for the default endpoint. If it differs, set **OIDC Endpoint** to the GitHub instance that runs the workflow, then save the Trust Provider so Aembit reads the issuer again.
* The token’s signature must verify against the keys Aembit fetches from the **OIDC Endpoint** on the Trust Provider. If it fails, confirm **OIDC Endpoint** points at the GitHub instance that runs the workflow.
* The token must be inside the validity window its `exp` and `nbf` claims set, with 5 minutes of clock skew allowed. If it has expired, request a new token and retry.

The `actor`, `repository`, and `workflow` attributes compare the token’s claims of the same name. A match rule on a claim the token doesn’t carry fails.

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

For this platform, **GitHub ID Token Subject** and **GitHub ID Token Repository** compare the token’s `sub` and `repository` claims.

## How to find your Edge SDK Client ID

1. Log in to your Aembit Tenant.

2. Go to the **Trust Providers** section in the left sidebar.

3. Select the Trust Provider you want to use for Edge API authentication.

4. In the **TRUST PROVIDER** section, find the **Edge SDK Client ID** field.

5. Copy the Edge SDK Client ID to use in your authentication requests.

   ![Aembit UI Trust Provider page](https://docs.aembit.io/_astro/edge-sdk-client-id.BJB7d1dG_H47oi.webp)
