---
type: how-to
title: "Use the Edge SDK with a Kubernetes service account"
description: "Retrieve credentials from a Kubernetes pod using the Edge SDK and the Kubernetes Service Account Trust Provider"
resource: https://docs.aembit.io/dev-guide/sdk/edge/integrations/kubernetes/
interface: sdk
tags: ["integration", "edge", "sdk"]
timestamp: 2026-10-15T12:57:18-07:00
---

# Use the Edge SDK with a Kubernetes service account

An application in a Kubernetes pod can prove its identity with the service account token that Kubernetes mounts into the pod. The Edge SDK reads that token and presents it to your Trust Provider in Aembit, which verifies it. You configure the SDK client and request a credential.

Use the SDK when you can change the application’s code and want credential retrieval explicit in it. To get credentials into a pod without code changes, [deploy Aembit Edge to Kubernetes](../../../../user-guide/deploy-install/kubernetes/kubernetes.md) instead.

## How a pod proves its identity

Kubernetes mounts a service account token into a pod at `/var/run/secrets/kubernetes.io/serviceaccount/token`. Kubernetes signs that token with the cluster’s private key, and the token names the pod’s namespace, pod, and service account.

Your application, or the Python SDK when you pass no token, reads the token, and the SDK sends it to Aembit. Aembit validates the signature with the attestation method on the Trust Provider. The default method, OIDC Discovery, retrieves the cluster’s public keys from the OIDC endpoint you configure. The Trust Provider also accepts an uploaded JWKS or an uploaded public key. It then evaluates the match rules on your Trust Provider against the token’s claims. Because Kubernetes issues the token to the pod, no credential has to exist in the application ahead of time.

For the request shape behind that exchange, see [Kubernetes attestation](../../../api/edge/endpoints/auth.md#kubernetes-attestation) in the Edge API reference.

## Before you start

* An Aembit Tenant with at least Read Only permission for Trust Providers.
* An Access Policy for the service your application reaches.
* A [Kubernetes Service Account Trust Provider](../../../../user-guide/access-policies/trust-providers/kubernetes-service-account-trust-provider.md) with at least one match rule on the `iss` claim, the `sub` claim, or the namespace, pod name, or service account name of your pod. With the OIDC Discovery attestation method, the Trust Provider also holds your cluster’s OIDC issuer URL.
* A Client Workload identified by your pod’s [Kubernetes Namespace](../../../../user-guide/access-policies/client-workloads/identification/kubernetes-namespace.md), [Kubernetes Service Account Name](../../../../user-guide/access-policies/client-workloads/identification/kubernetes-service-account-name.md), or [Kubernetes Service Account UID](../../../../user-guide/access-policies/client-workloads/identification/kubernetes-service-account-uid.md). Aembit reads these values from the service account token the SDK sends.
* A pod with its service account token mounted, running Node.js 20 or later for TypeScript, or Python 3.10 or later for Python. Kubernetes mounts the token unless the pod or its service account sets `automountServiceAccountToken: false`.

## Get your Edge SDK Client ID

Your application needs the Edge SDK Client ID from the [Kubernetes Service Account Trust Provider](../../../../user-guide/access-policies/trust-providers/kubernetes-service-account-trust-provider.md) whose match rules identify your pod.

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

The example collects every value you change into one `EXAMPLE_CONFIG` object at the top of the file. The two SDKs read the token in different ways.

The TypeScript Trust Provider takes the token as `serviceAccountToken`, either as a string or as a function that returns it. The example passes a function that reads the token file, so the SDK reads the current token each time it collects the pod’s identity. When `AEMBIT_K8S_SERVICE_ACCOUNT_TOKEN` holds a value, the function returns that value instead, for testing outside a cluster.

The Python Trust Provider reads the token file itself each time it collects the pod’s identity. Pass `token_path` to read a different file, or `token` to supply the token as a string or as a function that returns it. The example passes `AEMBIT_K8S_SERVICE_ACCOUNT_TOKEN` as `token` when it holds a value, for testing outside a cluster. It passes `K8S_TOKEN_PATH` as `token_path`, and the SDK reads the default token file when that variable is empty.

* TypeScript

  ```typescript
  import * as fs from "node:fs/promises"
  import { EdgeClient, trustProviders } from "@aembit/edge-sdk"


  const EXAMPLE_CONFIG = {
    baseUrl: "https://<tenant>.ec.<stack>.aembit.io",
    clientId: "your-edge-sdk-client-id",
    serverHost: "target.example.com",
    serverPort: 443,
    credentialType: "ApiKey",
    resourceSet: undefined as string | undefined,
    tokenFilePath: process.env.K8S_TOKEN_PATH || "/var/run/secrets/kubernetes.io/serviceaccount/token"
  }


  async function resolveKubernetesServiceAccountToken(): Promise<string> {
    const envToken = process.env.AEMBIT_K8S_SERVICE_ACCOUNT_TOKEN?.trim()
    if (envToken) {
      return envToken
    }


    return (await fs.readFile(EXAMPLE_CONFIG.tokenFilePath, "utf8")).trim()
  }


  const trustProvider = trustProviders.k8sServiceAccount({
    serviceAccountToken: () => resolveKubernetesServiceAccountToken()
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
      JSON.stringify({
        credentialType: credential.credentialType ?? null,
        expiresAt: credential.expiresAt ?? null,
        dataKeys: Object.keys(credential.data)
      })
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
  from aembit_edge.trust_providers import KubernetesServiceAccountTrustProvider


  EXAMPLE_CONFIG: dict[str, Any] = {
      "base_url": "https://<tenant>.ec.<stack>.aembit.io",
      "client_id": "your-edge-sdk-client-id",
      "server_host": "target.example.com",
      "server_port": 443,
      "credential_type": "ApiKey",
      "resource_set": None,
  }




  def main() -> None:
      trust_provider = KubernetesServiceAccountTrustProvider(
          token=os.environ.get("AEMBIT_K8S_SERVICE_ACCOUNT_TOKEN", "").strip() or None,
          token_path=os.environ.get("K8S_TOKEN_PATH", "").strip(),
      )


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

Where each value comes from:

| TypeScript value              | Python value                    | Where it comes from                                                                                                                                                                                    |
| ----------------------------- | ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `baseUrl`                     | `base_url`                      | The Edge API base URL in your Aembit Tenant profile. See [Get your Aembit Edge API base URL](../../../api/edge/quickstart-edge.md#get-your-aembit-edge-api-base-url).                                   |
| `clientId`                    | `client_id`                     | The Edge SDK Client ID you copied from the Trust Provider.                                                                                                                                             |
| `serverHost` and `serverPort` | `server_host` and `server_port` | The **Host** and **Port** under **Service Endpoint** on your Server Workload, which Aembit uses to find the Access Policy. **Port** must be equal, and **Host** must be equal or match a `*` wildcard. |
| `credentialType`              | `credential_type`               | The credential type your Credential Provider returns.                                                                                                                                                  |
| `resourceSet`                 | `resource_set`                  | Leave unset unless your Access Policy lives in a non-default Resource Set. It takes the Resource Set ID, not its name.                                                                                 |
| `tokenFilePath`               | `token_path`                    | The Python SDK defaults `token_path` to `/var/run/secrets/kubernetes.io/serviceaccount/token`. The TypeScript example sets that path. Both examples read `K8S_TOKEN_PATH`.                             |

> **`baseUrl` redirects**
>
> A `baseUrl` that redirects can break Edge SDK requests. A `301`, `302`, or `303` redirect turns each request into a `GET` without its body. In TypeScript, a `307` or `308` redirect to another host lets authentication succeed, then drops the `Authorization` header from the credential request, so Aembit returns `401`. In Python, a `307` or `308` redirect fails with that status code. Use the Edge API base URL from your profile exactly, without a trailing slash.

## Run the example

The runnable examples in the SDK repository, in [TypeScript](https://github.com/Aembit/edge-sdks/tree/main/ts/examples/k8s-service-account) and [Python](https://github.com/Aembit/edge-sdks/tree/main/py/examples/kubernetes_service_account), cover how to run each one in a pod. To test an example outside a cluster, supply a token in `AEMBIT_K8S_SERVICE_ACCOUNT_TOKEN`.

## Troubleshooting

Every error the Edge SDK raises carries a `kind`, which narrows the failure to one side of the exchange. Alongside `kind`, each error carries `statusCode`, `apiCode`, and `requestId` when Aembit supplies them, and `retryable` to report whether another attempt might succeed. The Python SDK names these fields `status_code`, `api_code`, `request_id`, and `retryable`.

### The SDK can’t read the service account token

The SDK raises a `trust_provider` error when it can’t get a non-empty service account token, and it never contacts Aembit. Confirm the token file exists at `/var/run/secrets/kubernetes.io/serviceaccount/token` inside the pod, or at the `token_path` you pass in Python. If it’s missing, the pod spec or its service account sets `automountServiceAccountToken: false`. Set `automountServiceAccountToken: true` in the pod spec, which overrides the service account’s setting, and recreate the pod. To test outside a cluster, pass the token yourself, as the examples do with `AEMBIT_K8S_SERVICE_ACCOUNT_TOKEN`.

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

For a Kubernetes Service Account Trust Provider, the validation in the fourth check verifies the token’s signature and expiry with the **Method** under **Attestation Method**:

* With **OIDC Discovery**, each **OIDC Endpoint** must be your cluster’s service account issuer base URL, without the `.well-known` path.
* With **Upload JWKS** or **Upload Public Key**, the key must be the public key your cluster signs service account tokens with.
* **Symmetric Key** accepts only tokens signed with an HMAC algorithm, and Kubernetes signs service account tokens with a private key, so every service account token fails. Switch the **Method** to **OIDC Discovery**, **Upload JWKS**, or **Upload Public Key**.
* **Aembit Tenant OIDC ID Token** appears under **Method**, but this Trust Provider doesn’t verify service account tokens against your Aembit Tenant’s signing keys, so this method can’t attest a pod. Switch the **Method** to **OIDC Discovery**, **Upload JWKS**, or **Upload Public Key**.

Validation doesn’t check the token’s issuer or audience, so an `iss` match rule is what ties the Trust Provider to one cluster. The `iss` and `sub` attributes compare the token’s claims of the same name. The `kubernetes.io { namespace }`, `kubernetes.io { pod { name } }`, and `kubernetes.io { serviceaccount { name } }` attributes compare fields of the token’s `kubernetes.io` claim.

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

For this platform, **Kubernetes Namespace**, **Kubernetes Service Account Name**, and **Kubernetes Service Account UID** compare values from the pod’s service account token.
