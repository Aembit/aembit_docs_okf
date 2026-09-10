---
type: how-to
title: "Use the Edge SDK on Vercel Functions"
description: "Authenticate a Vercel Function with its OIDC token and retrieve a credential through the Aembit Edge SDK."
resource: https://docs.aembit.io/dev-guide/sdk/edge/integrations/vercel-oidc/
interface: sdk
tags: ["integration", "edge", "sdk"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Use the Edge SDK on Vercel Functions

With OIDC federation turned on, Vercel signs a short-lived token for each function invocation and attaches it to the incoming request. The Edge SDK presents that token to Aembit as proof of identity, so a function can reach a protected service without holding a long-lived secret in its environment.

This guide covers the parts specific to Vercel. For what the SDK does in general, see the [Edge SDK overview](../overview.md).

## Before you start

On the Vercel side, enable OIDC federation on the project and run the function on the Node.js runtime.

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

## Write the function

Build the Trust Provider and the client inside the handler. The token belongs to a single request, so nothing exists to read at module load time. A client constructed there would capture a token that has already expired, or never existed at all:

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

Per-request construction gives up the in-memory token cache that a long-running process would reuse. That trade is correct here, because the identity itself is request-scoped.

The [runnable example in the SDK repository](https://github.com/Aembit/edge-sdks/tree/main/ts/examples/oidc-vercel-function) carries the full handler.

## Fill in your values

1. Set `baseUrl` to your tenant’s Edge host.

   The form is `https://<tenant>.ec.<stack>.aembit.io`. Use the final host. A URL that redirects fails against the credentials endpoint instead of following the redirect.

2. Set `clientId` to the Edge SDK Client ID from your OIDC ID Token Trust Provider.

3. Set `serverHost` and `serverPort` to the Service Endpoint on your Server Workload.

   These must match what you configured in Aembit exactly. A mismatch returns a response that looks successful but carries no credential.

4. Set `credentialType` to the type your Credential Provider returns, such as `ApiKey`.

If your tenant uses Resource Sets, pass `resourceSet` to `EdgeClient` as well.

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

**The function reports a missing token.** In production, confirm the project has OIDC federation enabled. Locally, confirm `vercel env pull` ran in the function’s project directory and that the resulting `.env.local` contains `VERCEL_OIDC_TOKEN`.

**Aembit returns `401` from the credentials endpoint.** Confirm `baseUrl` is the final Edge host and that it returns no redirect. Then confirm the Trust Provider can validate the signature, and that the `iss`, `aud`, and `sub` match rules line up with the claims in a real token.

**You get `200` back, but the credential type is `Unknown` and `dataKeys` is empty.** The request reached Aembit and failed to match an Access Policy. `serverHost` and `serverPort` are the usual cause, followed by `credentialType`, the token claim matching on the Client Workload, and then `resourceSet` if your tenant uses one.

## How to find your Edge SDK Client ID

1. Log in to your Aembit Tenant.

2. Go to the **Trust Providers** section in the left sidebar.

3. Select the Trust Provider you want to use for Edge API authentication.

4. In the **TRUST PROVIDER** section, find the **Edge SDK Client ID** field.

5. Copy the Edge SDK Client ID to use in your authentication requests.

   ![Aembit UI Trust Provider page](https://docs.aembit.io/_astro/edge-sdk-client-id.BJB7d1dG_H47oi.webp)
