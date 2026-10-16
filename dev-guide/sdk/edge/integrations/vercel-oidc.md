---
type: how-to
title: "Use the Edge SDK on Vercel Functions"
description: "Authenticate a Vercel Function with its OIDC token and retrieve a credential through the Aembit Edge SDK."
resource: https://docs.aembit.io/dev-guide/sdk/edge/integrations/vercel-oidc/
interface: sdk
tags: ["integration", "edge", "sdk"]
timestamp: 2026-10-15T12:57:18-07:00
---

# Use the Edge SDK on Vercel Functions

With OIDC federation turned on, Vercel signs a short-lived token for each function invocation and attaches it to the incoming request. The Edge SDK presents that token to Aembit as proof of identity, so a function can reach a protected service without holding a long-lived secret in its environment.

This guide covers the parts specific to Vercel. For what the SDK does in general, see the [Edge SDK overview](../overview.md).

## Before you start

On the Vercel side, enable OIDC federation on the project and run the function on the Node.js runtime for TypeScript or the Python runtime for Python.

In Aembit, configure an Access Policy with these pieces:

* An [OIDC ID Token Trust Provider](../../../../user-guide/access-policies/trust-providers/oidc-id-token-trust-provider.md) carrying an Edge SDK Client ID.
* A Client Workload whose [identifier](../../../../user-guide/access-policies/client-workloads/identification/oidc-id-token.md) matches a claim in the Vercel token.
* A Server Workload whose Service Endpoint host and port match what your function requests.
* A Credential Provider that returns the credential type your function asks for.

This flow has a setup step the cloud provider methods skip. Aembit verifies the token signature itself, so it needs the public keys from Vercel before the first request can pass. The OIDC Discovery attestation method is the least work. Give it the issuer URL, and Aembit finds the provider configuration and public keys from there.

Read the issuer and audience values off a real token rather than guessing them. Both depend on your Vercel team, so deploy the function once, log the decoded token, and write the `iss` and `aud` values you see into the match rules.

## Where the token comes from

Vercel delivers the token in two different places depending on where the code runs.

In production, the token arrives on the request as the `x-vercel-oidc-token` header. Locally, `vercel env pull` writes a `.env.local` file containing `VERCEL_OIDC_TOKEN`, and `vercel dev` loads it from there. Handling both keeps one function working in both places:

* TypeScript

  ```typescript
  function resolveToken(request: Request): string {
    const header = request.headers.get("x-vercel-oidc-token")?.trim()
    if (header) {
      return header
    }


    const env = process.env.VERCEL_OIDC_TOKEN?.trim()
    if (env) {
      return env
    }


    throw new Error(
      "Missing Vercel OIDC token. Expected the x-vercel-oidc-token header in production, " +
        "or VERCEL_OIDC_TOKEN for local development."
    )
  }
  ```

* Python

  ```python
  import os
  from email.message import Message




  def resolve_token(headers: Message) -> str:
      header = (headers.get("x-vercel-oidc-token") or "").strip()
      if header:
          return header


      env = os.environ.get("VERCEL_OIDC_TOKEN", "").strip()
      if env:
          return env


      raise RuntimeError(
          "Missing Vercel OIDC token. Expected the x-vercel-oidc-token header in production, "
          "or VERCEL_OIDC_TOKEN for local development."
      )
  ```

## Write the function

Build the Trust Provider and the client inside the handler. The token belongs to a single request, so nothing exists to read at module load time. A client constructed there would capture a token that has already expired, or never existed at all:

* TypeScript

  ```typescript
  import { EdgeClient, trustProviders } from "@aembit/edge-sdk"


  const CONFIG = {
    baseUrl: "https://<tenant>.ec.<stack>.aembit.io",
    clientId: "your-edge-sdk-client-id",
    serverHost: "target.example.com",
    serverPort: 443,
    credentialType: "ApiKey"
  }


  export async function GET(request: Request) {
    const trustProvider = trustProviders.oidcIdToken({
      identityToken: () => resolveToken(request)
    })


    const client = new EdgeClient({
      baseUrl: CONFIG.baseUrl,
      clientId: CONFIG.clientId,
      trustProvider
    })


    const credential = await client.getCredential({
      server: { host: CONFIG.serverHost, port: CONFIG.serverPort },
      credentialType: CONFIG.credentialType
    })


    return Response.json({
      credentialType: credential.credentialType,
      expiresAt: credential.expiresAt,
      dataKeys: Object.keys(credential.data)
    })
  }
  ```

* Python

  ```python
  import json
  from http.server import BaseHTTPRequestHandler
  from typing import Any


  from aembit_edge import CredentialServerRef, EdgeClient, EdgeClientConfig, GetCredentialInput
  from aembit_edge.trust_providers import OidcIdTokenTrustProvider


  CONFIG: dict[str, Any] = {
      "base_url": "https://<tenant>.ec.<stack>.aembit.io",
      "client_id": "your-edge-sdk-client-id",
      "server_host": "target.example.com",
      "server_port": 443,
      "credential_type": "ApiKey",
  }




  class handler(BaseHTTPRequestHandler):
      def do_GET(self) -> None:
          trust_provider = OidcIdTokenTrustProvider(
              identity_token=lambda: resolve_token(self.headers)
          )


          client = EdgeClient(
              EdgeClientConfig(
                  base_url=CONFIG["base_url"],
                  client_id=CONFIG["client_id"],
                  trust_provider=trust_provider,
              )
          )


          credential = client.get_credential(
              GetCredentialInput(
                  server=CredentialServerRef(host=CONFIG["server_host"], port=CONFIG["server_port"]),
                  credential_type=CONFIG["credential_type"],
              )
          )


          body = {
              "credentialType": credential.credential_type,
              "expiresAt": credential.expires_at,
              "dataKeys": list(credential.data.keys()),
          }
          self.send_response(200)
          self.send_header("Content-Type", "application/json")
          self.end_headers()
          self.wfile.write(json.dumps(body).encode("utf-8"))
  ```

  List `aembit-edge-sdk` in the project’s `requirements.txt`.

Per-request construction gives up the in-memory token cache that a long-running process would reuse. That trade is correct here, because the identity itself is request-scoped.

The runnable examples in the SDK repository, in [TypeScript](https://github.com/Aembit/edge-sdks/tree/main/ts/examples/oidc-vercel-function) and [Python](https://github.com/Aembit/edge-sdks/tree/main/py/examples/oidc_vercel_function), carry the full handler.

## Fill in your values

The Python function uses the same names in snake case, such as `base_url` for `baseUrl`.

1. Set `baseUrl` to your Aembit Tenant’s Edge host.

   The form is `https://<tenant>.ec.<stack>.aembit.io`. Use the final host, because a URL that redirects can break the SDK’s requests to Aembit.

2. Set `clientId` to the Edge SDK Client ID from your OIDC ID Token Trust Provider.

3. Set `serverHost` and `serverPort` to the Service Endpoint on your Server Workload.

   **Port** must equal the port you configured, and **Host** must equal the host you configured or match its `*` wildcard. A mismatch returns a credential whose `credentialType` is `Unknown` and that holds no data, and the SDK raises no error.

4. Set `credentialType` to the type your Credential Provider returns, such as `ApiKey`.

If your Aembit Tenant uses Resource Sets, pass `resourceSet` to `EdgeClient` as well, or `resource_set` to `EdgeClientConfig` in Python.

## Run it locally

Pull the environment and start the dev server from the function’s project directory, so Vercel writes its local state and `.env.local` in the right place:

```shell
vercel env pull
vercel dev
```

Then call the function:

```shell
curl "http://localhost:3000/api"
```

A working flow reports the credential type and the keys in the payload without printing the secret:

```json
{
  "credentialType": "ApiKey",
  "expiresAt": "2026-03-10T19:19:09.2559713Z",
  "dataKeys": ["apiKey"]
}
```

## Troubleshooting

Every error the Edge SDK raises carries a `kind`, which narrows the failure to one side of the exchange. Alongside `kind`, each error carries `statusCode`, `apiCode`, and `requestId` when Aembit supplies them, and `retryable` to report whether another attempt might succeed.

### The function reports a missing token

In production, confirm the project has OIDC federation enabled. Locally, confirm `vercel env pull` ran in the function’s project directory and that the resulting `.env.local` contains `VERCEL_OIDC_TOKEN`.

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

For an OIDC ID Token Trust Provider, the validation in the fourth check covers two things:

* The token must be inside the validity window its `exp` and `nbf` claims set, with 5 minutes of clock skew allowed. If it has expired, request a new token and retry.
* The **Method** under **Attestation Method** must verify the token’s signature. With **OIDC Discovery**, each **OIDC Endpoint** must be the token issuer’s base URL, without the `.well-known` path. With **Upload JWKS** or **Upload Public Key**, the key must be the public key of the token’s signer. With **Symmetric Key**, the key must be the shared secret that signed the token. With **Aembit Tenant OIDC ID Token**, your Aembit Tenant must have issued the token.

Aembit checks the token’s issuer and audience only through match rules. The `sub`, `aud`, and `iss` attributes compare the token’s claims of the same name, and `custom_claim` compares the claim you name in **Key**. Decode a real token and compare each claim with the **Value** of its rule.

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

For this platform, **OIDC ID Token Subject**, **OIDC ID Token Issuer**, and **OIDC ID Token Audience** compare the token’s `sub`, `iss`, and `aud` claims.

## How to find your Edge SDK Client ID

1. Log in to your Aembit Tenant.

2. Go to the **Trust Providers** section in the left sidebar.

3. Select the Trust Provider you want to use for Edge API authentication.

4. In the **TRUST PROVIDER** section, find the **Edge SDK Client ID** field.

5. Copy the Edge SDK Client ID to use in your authentication requests.

   ![Aembit UI Trust Provider page](https://docs.aembit.io/_astro/edge-sdk-client-id.BJB7d1dG_H47oi.webp)
