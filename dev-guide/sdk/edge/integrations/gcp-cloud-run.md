---
type: how-to
title: "Use the Edge SDK on Google Cloud Run"
description: "Authenticate a Cloud Run function with a Google identity token and retrieve a credential through the Aembit Edge SDK."
resource: https://docs.aembit.io/dev-guide/sdk/edge/integrations/gcp-cloud-run/
interface: sdk
tags: ["integration", "edge", "sdk"]
timestamp: 2026-10-15T12:57:18-07:00
---

# Use the Edge SDK on Google Cloud Run

A Cloud Run function already has an identity. Google attaches a service account to the runtime and issues signed identity tokens for it through the metadata server. The Edge SDK presents that token as proof of who the workload is, so your function needs no secret to authenticate with Aembit.

This guide covers the parts specific to Cloud Run. For what the SDK does in general, see the [Edge SDK overview](../overview.md).

## Before you start

On the Google side, you need a Cloud Run runtime with a service account attached. The function reaches the metadata server over the internal network, so that path needs no extra configuration.

In Aembit, configure an Access Policy with these pieces:

* A Client Workload that uses the GCP Identity Token [Client Identifier](../../../../user-guide/access-policies/client-workloads/identification/gcp-identity-token.md).
* A [GCP Identity Token Trust Provider](../../../../user-guide/access-policies/trust-providers/gcp-identity-token-trust-provider.md).
* A Server Workload whose Service Endpoint host and port match what your function requests.
* A Credential Provider that returns the credential type your function asks for.

Both the Client Workload identifier and the Trust Provider match on the `email` claim in the Google token, which is the service account address.

## Where the token comes from

The function asks the metadata server for an identity token at call time:

```text
http://metadata.google.internal/computeMetadata/v1/instance/service-accounts/default/identity
```

The request has two requirements.

The request needs the `Metadata-Flavor: Google` header. Google rejects the request otherwise, which keeps a stray browser or proxy request from reaching instance credentials.

The request also needs an `audience` query parameter, because Google issues no token without one. Set it to your Aembit identity host:

```text
https://<tenant>.id.<stack>.aembit.io
```

Aembit rejects a token whose audience isn’t this host, which the GCP Identity Token Trust Provider shows as **Edge SDK Audience**. Aembit identifies the workload by the token’s `email` claim.

The audience host uses the `id` subdomain, and `baseUrl` uses the `ec` subdomain. Check both values before you deploy.

## Write the function

Create the Trust Provider and the client once, outside the handler. Cloud Run reuses warm instances, so a client built at module scope keeps the in-memory access token across invocations instead of authenticating again on every request. Passing `identityToken` a function rather than a string makes that safe, because the SDK then fetches a fresh token only when it needs one. In Python, the parameter is `identity_token`.

* TypeScript

  ```typescript
  import { EdgeClient, trustProviders } from "@aembit/edge-sdk"


  const CONFIG = {
    baseUrl: "https://<tenant>.ec.<stack>.aembit.io",
    clientId: "your-edge-sdk-client-id",
    serverHost: "target.example.com",
    serverPort: 443,
    credentialType: "ApiKey",
    audience: "https://<tenant>.id.<stack>.aembit.io"
  }


  const trustProvider = trustProviders.gcpIdentityToken({
    identityToken: () => fetchIdentityToken(CONFIG.audience)
  })


  const client = new EdgeClient({
    baseUrl: CONFIG.baseUrl,
    clientId: CONFIG.clientId,
    trustProvider
  })


  export async function aembitGcpIdentityToken(req, res) {
    const credential = await client.getCredential({
      server: { host: CONFIG.serverHost, port: CONFIG.serverPort },
      credentialType: CONFIG.credentialType
    })


    res.status(200).json({
      credentialType: credential.credentialType,
      expiresAt: credential.expiresAt,
      dataKeys: Object.keys(credential.data)
    })
  }


  async function fetchIdentityToken(audience: string): Promise<string> {
    const url = new URL(
      "http://metadata.google.internal/computeMetadata/v1/instance/service-accounts/default/identity"
    )
    url.searchParams.set("audience", audience)


    const response = await fetch(url, {
      headers: { "Metadata-Flavor": "Google" }
    })


    if (!response.ok) {
      throw new Error(`Metadata server returned ${response.status}`)
    }


    return (await response.text()).trim()
  }
  ```

* Python

  ```python
  import urllib.parse
  import urllib.request
  from typing import Any


  import functions_framework
  from aembit_edge import CredentialServerRef, EdgeClient, EdgeClientConfig, GetCredentialInput
  from aembit_edge.trust_providers import GcpIdentityTokenTrustProvider


  CONFIG: dict[str, Any] = {
      "base_url": "https://<tenant>.ec.<stack>.aembit.io",
      "client_id": "your-edge-sdk-client-id",
      "server_host": "target.example.com",
      "server_port": 443,
      "credential_type": "ApiKey",
      "audience": "https://<tenant>.id.<stack>.aembit.io",
  }


  METADATA_IDENTITY_URL = (
      "http://metadata.google.internal/computeMetadata/v1/instance/service-accounts/default/identity"
  )




  def fetch_identity_token() -> str:
      query = urllib.parse.urlencode({"audience": CONFIG["audience"]})
      request = urllib.request.Request(
          f"{METADATA_IDENTITY_URL}?{query}",
          headers={"Metadata-Flavor": "Google"},
      )
      with urllib.request.urlopen(request, timeout=2.0) as response:
          body: bytes = response.read()
      return body.decode("utf-8").strip()




  trust_provider = GcpIdentityTokenTrustProvider(identity_token=fetch_identity_token)


  client = EdgeClient(
      EdgeClientConfig(
          base_url=CONFIG["base_url"],
          client_id=CONFIG["client_id"],
          trust_provider=trust_provider,
      )
  )




  @functions_framework.http
  def aembit_gcp_identity_token(request: Any) -> dict[str, Any]:
      credential = client.get_credential(
          GetCredentialInput(
              server=CredentialServerRef(host=CONFIG["server_host"], port=CONFIG["server_port"]),
              credential_type=CONFIG["credential_type"],
          )
      )


      return {
          "credentialType": credential.credential_type,
          "expiresAt": credential.expires_at,
          "dataKeys": list(credential.data.keys()),
      }
  ```

  List `functions-framework` and `aembit-edge-sdk` in the function’s `requirements.txt`.

The runnable examples in the SDK repository, in [TypeScript](https://github.com/Aembit/edge-sdks/tree/main/ts/examples/gcp-identity-token-function) and [Python](https://github.com/Aembit/edge-sdks/tree/main/py/examples/gcp_identity_token_function), add the error handling this excerpt trims.

Set the function entry point to `aembitGcpIdentityToken` for TypeScript or `aembit_gcp_identity_token` for Python when you create the function, in the console or through `gcloud`. Google routes requests to the registered handler by that name, so a mismatched entry point deploys and then fails at invocation.

## Fill in your values

The Python function uses the same names in snake case, such as `base_url` for `baseUrl`.

1. Set `baseUrl` to your Aembit Tenant’s Edge host.

   The form is `https://<tenant>.ec.<stack>.aembit.io`.

2. Set `clientId` to the Edge SDK Client ID from your GCP Identity Token Trust Provider.

3. Set `serverHost` and `serverPort` to the Service Endpoint on your Server Workload.

   **Port** must equal the port you configured, and **Host** must equal the host you configured or match its `*` wildcard. A mismatch returns a credential whose `credentialType` is `Unknown` and that holds no data, and the SDK raises no error.

4. Set `credentialType` to the type your Credential Provider returns, such as `ApiKey`.

5. Set `audience` to `https://<tenant>.id.<stack>.aembit.io`.

If your Aembit Tenant uses Resource Sets, pass `resourceSet` to `EdgeClient` as well, or `resource_set` to `EdgeClientConfig` in Python.

## Check the result

Invoke the function and read the JSON it returns. A working flow reports the credential type and the keys in the payload without printing the secret:

```json
{
  "credentialType": "ApiKey",
  "expiresAt": "2026-03-10T19:19:09.2559713Z",
  "dataKeys": ["apiKey"]
}
```

## Troubleshooting

Every error the Edge SDK raises carries a `kind`, which narrows the failure to one side of the exchange. Alongside `kind`, each error carries `statusCode`, `apiCode`, and `requestId` when Aembit supplies them, and `retryable` to report whether another attempt might succeed.

### The function can’t get a token

Confirm the runtime has a service account attached and that the metadata request carries the `Metadata-Flavor: Google` header.

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

For a GCP Identity Token Trust Provider, the validation in the fourth check includes these checks:

* The token’s audience must equal the **Edge SDK Audience** on the Trust Provider. If it differs, set `audience` in the metadata request to that value.
* The token’s issuer must be `https://accounts.google.com`, which is the issuer of tokens from the metadata server.
* The token must be inside the validity window its `exp` and `nbf` claims set, with 5 minutes of clock skew allowed. If it has expired, request a new token and retry.

The `email` attribute compares the token’s `email` claim, which holds the service account email. A match rule on a claim the token doesn’t carry fails.

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

For this platform, **GCP Identity Token** compares the token’s `email` claim.

## How to find your Edge SDK Client ID

1. Log in to your Aembit Tenant.

2. Go to the **Trust Providers** section in the left sidebar.

3. Select the Trust Provider you want to use for Edge API authentication.

4. In the **TRUST PROVIDER** section, find the **Edge SDK Client ID** field.

5. Copy the Edge SDK Client ID to use in your authentication requests.

   ![Aembit UI Trust Provider page](https://docs.aembit.io/_astro/edge-sdk-client-id.BJB7d1dG_H47oi.webp)
