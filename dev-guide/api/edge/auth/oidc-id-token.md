---
type: how-to
title: "Edge API authentication with OIDC ID Token"
description: "Authenticating with Aembit Edge API using an ID token from any OIDC-compliant identity provider"
resource: https://docs.aembit.io/dev-guide/api/edge/auth/oidc-id-token/
interface: api
tags: ["auth", "edge", "api"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Edge API authentication with OIDC ID Token

The `oidc` attestation method accepts an ID token from any OpenID Connect provider. Use it when your platform issues OIDC tokens to workloads but has no dedicated attestation method of its own.

GitHub Actions, GitLab Jobs, and Terraform Cloud each have a dedicated method and their own Trust Provider type.

Reach for `oidc` for everything else, such as a serverless platform that injects an OIDC token into each request or an identity provider you run yourself.

## Prerequisites

To authenticate using an OIDC ID token, you must have the following:

* Your Trust Provider’s Edge SDK Client ID ([how to find it](#how-to-find-your-edge-sdk-client-id))
* An [OIDC ID Token Trust Provider](../../../../user-guide/access-policies/trust-providers/oidc-id-token-trust-provider.md) in Aembit
* A way for Aembit to get your provider’s signing keys. The Trust Provider offers four attestation methods: OIDC Discovery, Symmetric Key, Upload JWKS, and Upload Public Key.
* An ID token whose claims satisfy the match rules on that Trust Provider

Unlike the cloud provider methods, this one has a setup dependency you can’t skip. Aembit validates the token signature itself, so supply your provider’s public keys before you send the first request. If you pick OIDC Discovery, Aembit must be able to reach your provider’s discovery endpoint over the internet. For a provider that isn’t publicly reachable, upload the JWKS or the public key directly instead.

## Authenticate with an OIDC ID token

To authenticate with the Aembit Edge API using an OIDC ID token, follow these steps:

1. Get an ID token from your identity provider.

   How you do this depends on the platform. On Vercel Functions, for example, the token arrives in the `x-vercel-oidc-token` request header. Check your provider’s documentation for where it puts the token.

   Whatever the source, the value must be a signed JWT on a single line, with no line breaks or surrounding whitespace.

2. Decode the token payload and compare it against your match rules.

   You should see something similar to the following:

   ```json
   {
     "aud": "aembit-prod-api-access",
     "iss": "https://identity-provider.my-company.com",
     "sub": "workload-id-98765",
     "exp": 1770000000,
     "iat": 1769996400
   }
   ```

   Your Trust Provider can match on `aud`, `iss`, `sub`, or any custom claim the token carries. Every match rule you configure must match, so confirm each one is present with the value you expect before you send the request.

3. Construct the authentication request payload using the `clientId` and the ID token.

   It should look something like this:

   ```shell
   {
       "clientId": "<edge-sdk-client-id>",
       "client": {
               "oidc": {
                   "identityToken": "<oidc-id-token>"
               }
        }
   }
   ```

4. Send the authentication request to your Aembit Edge API endpoint:

   ```shell
   curl --location 'https://<your-aembit-edge-url>/edge/v1/auth' \
   --header 'Content-Type: application/json' \
   --data '{
       "clientId": "your-edge-sdk-client-id",
       "client": {
           "oidc": {
               "identityToken": "eyJhbGciOiJSUzI1NiIsImtpZCI6IjZjZTk1MWQ4NTNkMWQ0YTJlNjQxMWQ..."
           }
       }
   }'
   ```

   When successful, you’ll receive output similar to:

   ```json
   {
       "accessToken": "eyJhbGciOiJSUzI1NiIsImtpZCI6IkpyR3JLQ0x6RVFN...",
       "tokenType": "Bearer",
       "expiresIn": 3600
   }
   ```

5. Use the `accessToken` as the `bearerToken` in subsequent API calls to authenticate your requests. This token is valid for the duration specified in `expiresIn` (in seconds).

   If the request fails, check the signing keys first. A token that decodes cleanly and matches every rule still fails when Aembit can’t reach the discovery endpoint or when the uploaded JWKS no longer holds the key that signed the token.

## How to find your Edge SDK Client ID

1. Log in to your Aembit Tenant.

2. Go to the **Trust Providers** section in the left sidebar.

3. Select the Trust Provider you want to use for Edge API authentication.

4. In the **TRUST PROVIDER** section, find the **Edge SDK Client ID** field.

5. Copy the Edge SDK Client ID to use in your authentication requests.

   ![Aembit UI Trust Provider page](https://docs.aembit.io/_astro/edge-sdk-client-id.BJB7d1dG_H47oi.webp)
