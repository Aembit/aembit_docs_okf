---
type: how-to
title: "Use the Edge SDK in GitLab CI/CD"
description: "Authenticate a GitLab CI/CD job with its OIDC ID token and retrieve a credential through the Aembit Edge SDK."
resource: https://docs.aembit.io/dev-guide/sdk/edge/integrations/gitlab-ci/
interface: sdk
tags: ["integration", "edge", "sdk"]
timestamp: 2026-10-15T12:57:18-07:00
---

# Use the Edge SDK in GitLab CI/CD

A GitLab CI/CD job can ask GitLab for a signed OpenID Connect (OIDC) ID token that describes the job and its project. The Edge SDK presents that token to a GitLab Job ID Token Trust Provider in Aembit, so a program in the job needs no stored secret to authenticate with Aembit.

This guide covers the parts specific to GitLab CI/CD. For what the SDK does in general, see the [Edge SDK overview](../overview.md).

If your job needs credentials in shell commands rather than in your own program, the [Aembit GitLab CI/CD component or the Aembit CLI](../../../../user-guide/deploy-install/ci-cd/gitlab/overview.md) retrieves them without code. Use the Edge SDK when a TypeScript or Python program in the job requests the credential itself.

## Before you start

On the GitLab side, you need GitLab CI/CD 15.7 or later, which provides OIDC ID tokens to jobs through the `id_tokens` keyword. The Python example needs Python 3.10 or later.

In Aembit, configure an Access Policy with these pieces:

* A [GitLab Job ID Token Trust Provider](../../../../user-guide/access-policies/trust-providers/gitlab-trust-provider.md) carrying an Edge SDK Client ID.
* A Client Workload whose identifier matches the job, such as [GitLab ID Token Project Path](../../../../user-guide/access-policies/client-workloads/identification/gitlab-id-token-project-path.md) or [GitLab ID Token Subject](../../../../user-guide/access-policies/client-workloads/identification/gitlab-id-token-subject.md).
* A Server Workload whose Service Endpoint host and port match what your program requests.
* A Credential Provider that returns the credential type your program asks for.

## Where the token comes from

A job requests the token with the `id_tokens` keyword in `.gitlab-ci.yml`. GitLab signs the token and exposes it to the job as an environment variable named after the key you declare. Add this block to the job that runs your program:

**.gitlab-ci.yml**

```yaml
id_tokens:
  GITLAB_OIDC_TOKEN:
    aud: https://<tenant>.id.<stack>.aembit.io
```

Set `aud` to the **Edge SDK Audience** value shown on your GitLab Job ID Token Trust Provider, which takes the form `https://<tenant>.id.<stack>.aembit.io`. Aembit rejects a token whose audience doesn’t match. The audience host uses the `id` subdomain, and `baseUrl` uses the `ec` subdomain.

Both examples read `GITLAB_OIDC_TOKEN`. The TypeScript example checks `AEMBIT_GITLAB_OIDC_TOKEN` first, so you can declare the token under that name instead.

## Write the program

The Trust Provider accepts the token as a string or as a function that returns one. The TypeScript example passes a function, which the SDK calls on every `getCredential()` and `authenticate()` call. The Python example reads the environment variable once and passes the string. Either way, the SDK rejects an empty token with the `trust_provider` error kind before it contacts Aembit.

* TypeScript

  ```typescript
  import { EdgeClient, TrustProviderError, trustProviders } from "@aembit/edge-sdk"


  const EXAMPLE_CONFIG = {
    baseUrl: "https://<tenant>.ec.<stack>.aembit.io",
    clientId: "your-edge-sdk-client-id",
    serverHost: "target.example.com",
    serverPort: 443,
    credentialType: "ApiKey",
    resourceSet: undefined as string | undefined
  }


  function resolveGitLabIdentityToken(): string {
    const token = process.env.AEMBIT_GITLAB_OIDC_TOKEN?.trim() || process.env.GITLAB_OIDC_TOKEN?.trim()


    if (token) {
      return token
    }


    throw new TrustProviderError(
      "Missing GitLab CI identity token. Ensure id_tokens are configured in .gitlab-ci.yml.",
      { retryable: false }
    )
  }


  const trustProvider = trustProviders.gitlabIdentityToken({
    identityToken: () => resolveGitLabIdentityToken()
  })


  const client = new EdgeClient({
    baseUrl: EXAMPLE_CONFIG.baseUrl,
    clientId: EXAMPLE_CONFIG.clientId,
    trustProvider,
    resourceSet: EXAMPLE_CONFIG.resourceSet
  })


  async function main() {
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
  import os
  from typing import Any


  from aembit_edge import CredentialServerRef, EdgeClient, EdgeClientConfig, GetCredentialInput
  from aembit_edge.errors import TrustProviderError
  from aembit_edge.trust_providers import GitLabTrustProvider


  EXAMPLE_CONFIG: dict[str, Any] = {
      "base_url": "https://<tenant>.ec.<stack>.aembit.io",
      "client_id": "your-edge-sdk-client-id",
      "resource_set": None,
      "gitlab_token_env_var": "GITLAB_OIDC_TOKEN",
      "server_host": "target.example.com",
      "server_port": 443,
      "credential_type": "ApiKey",
  }




  def resolve_gitlab_identity_token() -> str:
      env_var = EXAMPLE_CONFIG["gitlab_token_env_var"]
      token = os.environ.get(env_var, "").strip()
      if token:
          return token


      raise TrustProviderError(
          f"GitLab OIDC token could not be resolved from environment variable: {env_var}. "
          "Ensure you have declared the 'id_tokens' block in your .gitlab-ci.yml.",
          retryable=False,
      )




  def main() -> None:
      trust_provider = GitLabTrustProvider(identity_token=resolve_gitlab_identity_token())


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

The runnable examples in the SDK repository, in [TypeScript](https://github.com/Aembit/edge-sdks/tree/main/ts/examples/gitlab-ci-oidc) and [Python](https://github.com/Aembit/edge-sdks/tree/main/py/examples/gitlab_ci), add the error handling this excerpt trims, along with a sample pipeline job for each language.

## Fill in your values

Set each value in `EXAMPLE_CONFIG` at the top of the program:

| TypeScript value              | Python value                    | Where it comes from                                                                                                                                                                                    |
| ----------------------------- | ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `baseUrl`                     | `base_url`                      | The Edge API base URL in your Aembit Tenant profile. See [Get your Aembit Edge API base URL](../../../api/edge/quickstart-edge.md#get-your-aembit-edge-api-base-url).                                   |
| `clientId`                    | `client_id`                     | The Edge SDK Client ID from your GitLab Job ID Token Trust Provider. See [How to find your Edge SDK Client ID](#how-to-find-your-edge-sdk-client-id).                                                  |
| `serverHost` and `serverPort` | `server_host` and `server_port` | The **Host** and **Port** under **Service Endpoint** on your Server Workload, which Aembit uses to find the Access Policy. **Port** must be equal, and **Host** must be equal or match a `*` wildcard. |
| `credentialType`              | `credential_type`               | The credential type your Credential Provider returns, such as `ApiKey`.                                                                                                                                |
| `resourceSet`                 | `resource_set`                  | Leave unset unless your Access Policy lives in a non-default Resource Set. It takes the Resource Set ID, not its name.                                                                                 |
| Not configurable              | `gitlab_token_env_var`          | The `id_tokens` key you declared in `.gitlab-ci.yml`. The TypeScript example reads `AEMBIT_GITLAB_OIDC_TOKEN`, then `GITLAB_OIDC_TOKEN`.                                                               |

Use the Edge API base URL from your profile exactly, without a trailing slash. A `baseUrl` that redirects can break Edge SDK requests. A `301`, `302`, or `303` redirect turns each request into a `GET` without its body. In TypeScript, a `307` or `308` redirect to another host lets authentication succeed, then drops the `Authorization` header from the credential request, so Aembit returns `401`. In Python, a `307` or `308` redirect fails with that status code.

## Check the result

Run the program in a job that declares the `id_tokens` block, and read the job log. A working flow reports the keys in the credential payload without printing the secret.

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

To test outside GitLab, export a valid GitLab ID token as `GITLAB_OIDC_TOKEN` and run the program locally.

## Troubleshooting

Every error the Edge SDK raises carries a `kind`, which narrows the failure to one side of the exchange. Alongside `kind`, each error carries `statusCode`, `apiCode`, and `requestId` when Aembit supplies them, and `retryable` to report whether another attempt might succeed. The Python SDK names these fields `status_code`, `api_code`, `request_id`, and `retryable`.

### The program reports a missing GitLab token

The example raises this error before it contacts Aembit. Confirm the job declares the `id_tokens` block, and that the key matches the variable name the program reads.

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

For a GitLab Job ID Token Trust Provider, the validation in the fourth check includes these checks:

* The token’s audience must equal the **Edge SDK Audience** on the Trust Provider. If it differs, set `aud` in the `id_tokens` block to that value.
* The token’s `iss` claim must equal the **OIDC Endpoint** on the Trust Provider, such as `https://gitlab.com`. If it differs, set **OIDC Endpoint** to the URL of the GitLab instance that runs the job.
* The token’s signature must verify against the keys Aembit fetches from the **OIDC Endpoint** on the Trust Provider. If it fails, confirm **OIDC Endpoint** points at the GitLab instance that runs the job.
* The token must be inside the validity window its `exp` and `nbf` claims set, with 5 minutes of clock skew allowed. If it has expired, request a new token and retry.

The `namespace_path`, `project_path`, and `ref_path` attributes compare the token’s claims of the same name, and `subject` compares the `sub` claim. A match rule on a claim the token doesn’t carry fails.

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

For this platform, **GitLab ID Token Subject**, **GitLab ID Token Project Path**, **GitLab ID Token Namespace Path**, and **GitLab ID Token Ref Path** compare the token’s `sub`, `project_path`, `namespace_path`, and `ref_path` claims.

## How to find your Edge SDK Client ID

1. Log in to your Aembit Tenant.

2. Go to the **Trust Providers** section in the left sidebar.

3. Select the Trust Provider you want to use for Edge API authentication.

4. In the **TRUST PROVIDER** section, find the **Edge SDK Client ID** field.

5. Copy the Edge SDK Client ID to use in your authentication requests.

   ![Aembit UI Trust Provider page](https://docs.aembit.io/_astro/edge-sdk-client-id.BJB7d1dG_H47oi.webp)
