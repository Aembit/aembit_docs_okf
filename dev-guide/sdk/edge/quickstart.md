---
type: how-to
title: "Get started with the Aembit Edge SDK"
description: "Configure Aembit in the console, install the TypeScript or Python Edge SDK, and retrieve a credential."
resource: https://docs.aembit.io/dev-guide/sdk/edge/quickstart/
interface: sdk
tags: ["edge", "sdk"]
timestamp: 2026-10-15T12:57:18-07:00
---

# Get started with the Aembit Edge SDK

In this quickstart, you configure Aembit in the console, install the Edge SDK for TypeScript or Python, and run a short program that retrieves a credential. The console side defines which workload may receive which credential, and the code side presents the workload’s identity and asks for it.

This guide walks you through the following primary steps:

1. [Configure your Aembit Tenant](#step-1-configure-your-aembit-tenant)
2. [Install the Edge SDK](#step-2-install-the-edge-sdk)
3. [Retrieve a credential](#step-3-retrieve-a-credential)

## Before you begin

This quickstart assumes you have a basic understanding of [how Aembit works](../../../get-started/how-aembit-works.md).

To follow this quickstart, you must have the following:

* The `tenantId` from your Aembit Tenant (`https://<tenantId>.aembit.io`).\
  *[Create a free Aembit Tenant](https://useast2.aembit.io/signup) if you don’t have one.*
* Node.js 20 or later for TypeScript, or Python 3.10 or later for Python.
* An OpenID Connect (OIDC) ID token that identifies the environment your code runs in.

Platforms such as GitHub Actions, GitLab CI, Kubernetes, and Vercel issue OIDC ID tokens. For this quickstart you paste one token into the program, so any OIDC ID token you can copy works. The [Edge API quickstart](../../api/edge/quickstart-edge.md#step-1-connect-aembit-to-github-or-gitlab) shows one way to generate and decode a token from a GitHub or GitLab CI job.

## Step 1: Configure your Aembit Tenant

In this step, you create the five Aembit components that authorize your program’s first credential:

* A Trust Provider that verifies the identity your code presents.
* A Client Workload that represents your program.
* A Credential Provider that supplies the credential.
* A Server Workload that represents the service the credential is for.
* An Access Policy that ties the other four components together.

### Create a Trust Provider

The [OIDC ID Token Trust Provider](../../../user-guide/access-policies/trust-providers/oidc-id-token-trust-provider.md) validates any standards-compliant OIDC ID token, which keeps this quickstart independent of where your code runs.

Read the issuer and subject values off a real token rather than guessing them. Decode your token, then copy the `iss` and `sub` claims you actually see into the following steps.

1. Log into your Aembit Tenant at `https://<tenantId>.aembit.io` and go to **Trust Providers** in the left sidebar menu.

   Click **+ New**.

2. Fill out the **Trust Provider** form:

   * **Name** - Enter a descriptive name like `Edge SDK Quickstart`.
   * **TRUST PROVIDER** - Select **OIDC ID Token**, which reveals its configuration options.
   * **Attestation Method** - Select **OIDC Discovery** and enter your identity provider’s main URL, which matches the token’s `iss` claim.
   * **Match Rules** - Select **`iss`** and enter the same issuer URL as the **Value**.

3. Click **Save**.

4. Click your new Trust Provider and copy the **Edge SDK Client ID** value, because your program passes it to Aembit with every authentication request.

   To find this value again later, see [Get your Edge SDK Client ID](../../../user-guide/access-policies/trust-providers/get-edge-sdk-client-id.md).

### Create a Client Workload

The Client Workload represents your program in the Access Policy, and Aembit matches it against the claims in your token.

1. While still in your Aembit Tenant, go to **Client Workloads** in the left sidebar menu.

   Click **+ New**.

2. Fill out the **Client Workload** form:

   * **Name** - Enter a descriptive name like `Edge SDK Quickstart App`.
   * **Client Identification** - Select **OIDC ID Token Subject** and enter your token’s `sub` claim as the **Value**.

3. Click **Save**.

### Create a Credential Provider

The Credential Provider defines the credential your program retrieves. This quickstart uses a static API key, because it needs no integration with an external secrets store.

1. While still in your Aembit Tenant, go to **Credential Providers** in the left sidebar menu.

   Click **+ New**.

2. Fill out the **Credential Provider** form:

   * **Name** - Enter a descriptive name like `My Service API Key`.
   * **Credential Type** - Select **API Key**.
   * **API Key** - Enter an API key for your target service, like `my_secure_api_key_abc123xyz789`.

3. Click **Save**.

### Create a Server Workload

The Server Workload represents the target service your program requests a credential for. For this quickstart it’s a placeholder rather than a real service, because the goal is to demonstrate the retrieval flow.

1. While still in your Aembit Tenant, go to **Server Workloads** in the left sidebar menu.

   Click **+ New**.

2. Fill out the **Server Workload** form’s **Service Endpoint** section:

   * **Host** - Enter `api.myservice.com`.
   * **Application Protocol** - Select **HTTP**, which sets the **Port** to its default of `80`.

   Leave the remaining fields at their default values.

   Record the host and port, because the values in your program must match them.

3. Click **Save**.

### Create an Access Policy

The Access Policy ties the four components together: it permits the Client Workload, verified by the Trust Provider, to receive the Credential Provider’s credential for the Server Workload. Without it, Aembit denies the credential request.

1. While still in your Aembit Tenant, go to **Access Policies** in the left sidebar menu.

   Click **+ New** to open the Access Policy Builder.

2. In the **Name** field, enter a descriptive name like `Edge SDK Quickstart Policy`, then click **Save**.

3. In the **Client Workload** card in the right panel, click **+ Configure**, then select the **Select Existing** tab.

   Select the Client Workload you created earlier and click **Use Selected**.

4. In the **Server Workload** card, click **+ Configure**, then select the **Select Existing** tab.

   Select the Server Workload you created earlier and click **Use Selected**.

5. In the **Trust Provider** card, click **+ Configure**, then select the **Select Existing** tab.

   Select the Trust Provider you created earlier and click **Use Selected**.

6. In the **Credential Provider** card, click **+ Configure**, then select the **Select Existing** tab.

   Select the Credential Provider you created earlier and click **Use Selected**.

7. Click **Save Policy & Activate**.

Your Aembit Tenant is now configured, and the remaining steps happen in code.

## Step 2: Install the Edge SDK

In your project directory, install the SDK from npm for TypeScript or from PyPI for Python:

* TypeScript

  ```shell
  npm install @aembit/edge-sdk
  ```

* Python

  ```shell
  pip install aembit-edge-sdk
  ```

## Step 3: Retrieve a credential

Create a file named `quickstart.ts` or `quickstart.py` with the following program:

* TypeScript

  **quickstart.ts**

  ```ts
  import { EdgeClient, trustProviders } from "@aembit/edge-sdk";


  const client = new EdgeClient({
    baseUrl: "https://<tenant>.ec.<stack>.aembit.io",
    clientId: "your-edge-sdk-client-id",
    trustProvider: trustProviders.oidcIdToken({
      identityToken: "your-oidc-id-token",
    }),
  });


  const credential = await client.getCredential({
    server: {
      host: "api.myservice.com",
      port: 80,
    },
    credentialType: "ApiKey",
  });


  console.log({
    credentialType: credential.credentialType,
    expiresAt: credential.expiresAt,
    dataKeys: Object.keys(credential.data),
  });
  ```

* Python

  **quickstart.py**

  ```py
  from aembit_edge import CredentialServerRef, EdgeClient, EdgeClientConfig, GetCredentialInput
  from aembit_edge.trust_providers import OidcIdTokenTrustProvider


  client = EdgeClient(
      EdgeClientConfig(
          base_url="https://<tenant>.ec.<stack>.aembit.io",
          client_id="your-edge-sdk-client-id",
          trust_provider=OidcIdTokenTrustProvider(
              identity_token="your-oidc-id-token",
          ),
      )
  )


  credential = client.get_credential(
      GetCredentialInput(
          server=CredentialServerRef(
              host="api.myservice.com",
              port=80,
          ),
          credential_type="ApiKey",
      )
  )


  print(
      {
          "credential_type": credential.credential_type,
          "expires_at": credential.expires_at,
          "data_keys": list(credential.data.keys()),
      }
  )
  ```

The program builds an `EdgeClient`, presents your OIDC ID token through the Trust Provider, and requests the credential your Access Policy grants. It prints the credential type and the keys in the payload rather than the secret itself.

### Fill in your values

The Python program uses the same names in snake case, such as `base_url` for `baseUrl`.

1. Set `baseUrl` to your Aembit Tenant’s Edge API base URL.

   To find it, hover over your username in the bottom left corner of your Aembit Tenant, select **Profile**, and copy the Edge API base URL. The form is `https://<tenant>.ec.<stack>.aembit.io`. Use this final host, because a URL that redirects can break the SDK’s requests to Aembit.

2. Set `clientId` to the Edge SDK Client ID you copied from your Trust Provider.

3. Set `identityToken` to your decoded OIDC ID token, which starts with `ey`.

   A static string suits this quickstart, because you run the program once. In a long-running application, pass a function that returns a fresh token instead, because OIDC ID tokens are short-lived. The examples in the SDK repository for [TypeScript](https://github.com/Aembit/edge-sdks/tree/main/ts/examples) and [Python](https://github.com/Aembit/edge-sdks/tree/main/py/examples) show per-platform Trust Providers that collect identity evidence automatically, with no token handling in your code.

4. Confirm `host` and `port` match the Service Endpoint on your Server Workload.

   **Port** must be equal, and **Host** must be equal or match a `*` wildcard. A mismatch returns a credential whose `credentialType` is `Unknown` and that holds no data, and the SDK raises no error.

5. Confirm `credentialType` is `ApiKey`, matching your Credential Provider.

### Run the program

Run the file with a TypeScript runner such as `tsx`, or with Python:

* TypeScript

  ```shell
  npx tsx quickstart.ts
  ```

  A working flow reports the credential type and the keys in the payload:

  **Output**

  ```shell
  {
    credentialType: 'ApiKey',
    expiresAt: '2026-08-26T19:19:09.2559713Z',
    dataKeys: [ 'apiKey' ]
  }
  ```

* Python

  ```shell
  python quickstart.py
  ```

  A working flow reports the credential type and the keys in the payload:

  **Output**

  ```shell
  {'credential_type': 'ApiKey', 'expires_at': '2026-08-26T19:19:09.2559713Z', 'data_keys': ['apiKey']}
  ```

The `apiKey` entry in the credential’s `data` holds the value you entered in your Credential Provider, and your program can now pass it to the target service.

## Troubleshooting

Every error the Edge SDK raises carries a `kind`, which narrows the failure to one side of the exchange. Alongside `kind`, each error carries `statusCode`, `apiCode`, and `requestId` when Aembit supplies them, and `retryable` to report whether another attempt might succeed.

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

**`credentialType` is `Unknown`.** Aembit authenticated the workload but returned no credential. Find the request’s event in **Reporting**, as [Retrieving Access Authorization Event data](../../../user-guide/audit-report/access-authorization-events.md#retrieving-access-authorization-event-data) describes. The event records a `result` for the Client Workload, the Server Workload, the Access Policy, each Trust Provider, and each Access Condition, and `NotFound` marks one Aembit couldn’t identify. Fix the first one that didn’t succeed:

* **Server Workload**: Aembit compares `server.host` and `server.port` with **Host** and **Port** under **Service Endpoint**, not with **Forward to Port**. **Host** matches when it equals `server.host`, and a `*` in **Host** matches any run of characters. Aembit doesn’t compare **Transport Protocol** or the **TLS** settings. The Server Workload must also be **Active** and belong to an active Access Policy.
* **Client Workload**: each identifier type under **Client Identification** must match this workload. Rows of the same type are alternatives, except **OIDC ID Token** rows with different **Claim Name** values, which must each match. The Client Workload must also be **Active** and belong to an active Access Policy. The identifier types that fit this platform appear at the end of this section.
* **Trust Provider** with the result `Unauthorized`: Aembit checks the workload’s evidence again on every credential request. Every active Trust Provider on the Access Policy must attest the workload, and Aembit stops at the first that fails. A Trust Provider this workload can’t satisfy, such as one for another platform, fails every request. Switch off its **Active** toggle, remove it from the Access Policy, or give the workload its own Access Policy. The event’s `reason` names the check that failed. For `MatchRuleFailed`, the event also records the `attribute`, `expectedValue` or `expectedValues`, and `actualValue`, so correct that match rule’s **Value**. When the Trust Provider validates an identity token and the event records no `actualValue`, Aembit couldn’t compare the rule. Give each match rule a **Value**, and confirm the token carries each claim the match rules name. For any other `reason`, work through the Trust Provider checks in the Authentication fails section of this page.
* **Access Condition** with the result `Unauthorized`: meet the condition, or remove it from the Access Policy.
* **Every entity succeeded**: confirm the Access Policy has a Credential Provider. When the event’s `credentialProvider` entry carries a `reason`, [Credential failure](../../../user-guide/audit-report/access-authorization-events.md#credential-failure) lists what each reason means.

When a Client Workload uses the **Aembit Client ID** identifier, the SDK doesn’t read the `CLIENT_WORKLOAD_ID` environment variable. Pass the value in your client configuration instead, as `clientWorkloadDetails: { os: { environment: { CLIENT_WORKLOAD_ID: "<value>" } } }`, or as the same structure in `client_workload_details` in Python.

For this platform, **OIDC ID Token Subject**, **OIDC ID Token Issuer**, and **OIDC ID Token Audience** compare the token’s `sub`, `iss`, and `aud` claims.

## What’s next

* Follow a runnable example in the SDK repository, in [TypeScript](https://github.com/Aembit/edge-sdks/tree/main/ts/examples) or [Python](https://github.com/Aembit/edge-sdks/tree/main/py/examples), to replace the pasted token with a Trust Provider that collects identity evidence from your platform automatically.
* Browse the [SDK repository](https://github.com/Aembit/edge-sdks) for the developer reference.
* See the [Edge API](../../api/edge/overview.md) for the REST interface the SDK wraps.
