---
type: how-to
title: "Get started with the Aembit Edge SDK"
description: "Configure Aembit in the console, install the TypeScript Edge SDK, and retrieve your first credential."
resource: https://docs.aembit.io/dev-guide/sdk/edge/quickstart/
interface: sdk
tags: ["edge", "sdk"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Get started with the Aembit Edge SDK

In this quickstart, you configure Aembit in the console, install the TypeScript Edge SDK, and run a short program that retrieves a credential. The console side defines which workload may receive which credential, and the code side presents the workload’s identity and asks for it.

This guide walks you through the following primary steps:

1. [Configure your Aembit Tenant](#step-1-configure-your-aembit-tenant)
2. [Install the Edge SDK](#step-2-install-the-edge-sdk)
3. [Retrieve a credential](#step-3-retrieve-a-credential)

## Before you begin

This quickstart assumes you have a basic understanding of [how Aembit works](../../../get-started/how-aembit-works.md).

To follow this quickstart, you must have the following:

* The `tenantId` from your Aembit Tenant (`https://<tenantId>.aembit.io`).\
  *[Create a free Aembit Tenant](https://useast2.aembit.io/signup) if you don’t have one.*
* Node.js 20 or later.
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

   Record the host and port, because the values in your program must match them exactly.

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

In your project directory, install the SDK from npm:

```shell
npm install @aembit/edge-sdk
```

## Step 3: Retrieve a credential

Create a file named `quickstart.ts` with the following program:

**quickstart.ts**

```ts
import { EdgeClient, trustProviders } from "@aembit/edge-sdk";


const client = new EdgeClient({
  baseUrl: "https://<tenant>.ec.<region>.aembit.io",
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

The program builds an `EdgeClient`, presents your OIDC ID token through the Trust Provider, and requests the credential your Access Policy grants. It prints the credential type and the keys in the payload rather than the secret itself.

### Fill in your values

1. Set `baseUrl` to your tenant’s Edge API base URL.

   To find it, hover over your username in the bottom left corner of your Aembit Tenant, select **Profile**, and copy the **Edge API Base URL** field. The form is `https://<tenant>.ec.<stack>.aembit.io`. Use this final host, because a URL that redirects fails against the credentials endpoint instead of following the redirect.

2. Set `clientId` to the Edge SDK Client ID you copied from your Trust Provider.

3. Set `identityToken` to your decoded OIDC ID token, which starts with `ey`.

   A static string suits this quickstart, because you run the program once. In a long-running application, pass a function that returns a fresh token instead, because OIDC ID tokens are short-lived. The [examples in the SDK repository](https://github.com/Aembit/edge-sdks/tree/main/ts/examples) show per-platform Trust Providers that collect identity evidence automatically, with no token handling in your code.

4. Confirm `host` and `port` match the Service Endpoint on your Server Workload exactly.

   A mismatch returns a response that looks successful but carries no credential.

5. Confirm `credentialType` is `ApiKey`, matching your Credential Provider.

### Run the program

Run the file with a TypeScript runner such as `tsx`:

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

The `apiKey` entry in `credential.data` holds the value you entered in your Credential Provider, and your program can now pass it to the target service.

## Troubleshooting

**Aembit returns `401` from the credentials endpoint.** Confirm `baseUrl` is the final Edge host and that it returns no redirect. Then confirm your token hasn’t expired, because OIDC ID tokens are short-lived, and generate a fresh one if needed. Finally, confirm the Trust Provider’s match rules line up with the claims in your real token.

**You get a response, but the credential type is `Unknown` and `dataKeys` is empty.** The request reached Aembit and failed to match a policy. Check `host` and `port` against the Server Workload first, then `credentialType`, then the `sub` claim matching on the Client Workload.

## What’s next

* Follow a [runnable example in the SDK repository](https://github.com/Aembit/edge-sdks/tree/main/ts/examples) to replace the pasted token with a Trust Provider that collects identity evidence from your platform automatically.
* Browse the [SDK repository](https://github.com/Aembit/edge-sdks) for the developer reference.
* See the [Edge API](../../api/edge/overview.md) for the REST interface the SDK wraps.
