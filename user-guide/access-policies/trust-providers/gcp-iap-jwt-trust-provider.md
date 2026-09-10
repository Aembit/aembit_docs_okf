---
type: explanation
title: "Google Cloud Identity-Aware Proxy (IAP) JWT Trust Provider"
description: "How to configure a Google Cloud Identity-Aware Proxy (IAP) JWT Trust Provider to validate signed tokens forwarded by IAP"
resource: https://docs.aembit.io/user-guide/access-policies/trust-providers/gcp-iap-jwt-trust-provider/
tags: ["trust-provider", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Google Cloud Identity-Aware Proxy (IAP) JWT Trust Provider

The Google Cloud Identity-Aware Proxy (IAP) JWT Trust Provider validates the signed JSON Web Token (JWT) that IAP forwards to your workloads. Use this Trust Provider when IAP authenticates users in front of your workloads on Google Kubernetes Engine (GKE), Cloud Run, or App Engine.

After IAP authenticates a user, it appends a signed `x-goog-iap-jwt-assertion` header to every request it forwards. This Trust Provider verifies that token, extracts the user identity, and evaluates your Access Policy before authorizing access.

Aembit resolves Google’s IAP signing keys for you, so your workload only forwards the token.

> **This isn’t the GCP Identity Token Trust Provider**
>
> This Trust Provider validates the **user** identity that IAP asserts in the `x-goog-iap-jwt-assertion` header. To validate the identity of a **workload** running in Google Cloud, use the [GCP Identity Token Trust Provider](gcp-identity-token-trust-provider.md) instead.

## Why IAP tokens need a dedicated Trust Provider

IAP tokens are signed JWTs, but they aren’t standard OpenID Connect (OIDC) ID tokens, so the [OIDC ID Token Trust Provider](oidc-id-token-trust-provider.md) can’t validate them. Two differences matter:

* **No OIDC discovery document.** Google publishes IAP signing keys at a static JWKS endpoint and doesn’t serve a `.well-known/openid-configuration` document, so there is no discovery URL to configure.
* **Non-standard issuer and audience.** The issuer is always `https://cloud.google.com/iap`, and the audience identifies the Google Cloud resource that IAP protects rather than an OIDC client ID.

This Trust Provider handles both differences for you. You supply the IAP audience, and Aembit pins the issuer and resolves the keys.

## How the GCP IAP JWT Trust Provider works

Aembit resolves Google’s signing key at validation time rather than from a static, preconfigured key set. During authentication, your workload and Aembit Edge perform a clear sequence of actions.

1. Your workload extracts the `x-goog-iap-jwt-assertion` header that IAP added to the request, then presents that token to Aembit Edge to prove the user’s identity.

2. Aembit confirms the token’s issuer is Google IAP and reads the key ID (`kid`) from the token header.

3. Aembit retrieves the public signing key for that `kid` from Google’s IAP key endpoint and validates the token’s signature against it. Aembit caches the keys in memory and fetches from Google only when it sees a signing key it doesn’t recognize.

4. Aembit compares the token’s audience against the IAP audience you configured. If the audience doesn’t match, Aembit denies the request.

5. Aembit evaluates the remaining claims against the match rules you configured, then authorizes the request and applies the relevant Access Policies. If any check fails, Aembit denies the request.

![How Aembit validates a GCP IAP JWT: the workload forwards the IAP assertion, and Aembit resolves Google's signing key, validates the token and audience, and evaluates the Access Policy](https://docs.aembit.io/d2/docs/user-guide/access-policies/trust-providers/gcp-iap-jwt-trust-provider-0.svg)

## Match rules

You authorize access by matching on the claims in the IAP token. Each match rule pairs an **Attribute** with the **Value** you expect that claim to have.

The following table describes the attributes available for the GCP IAP JWT Trust Provider:

| Attribute      | Description                                                                                                                                                                                                                                                                                                                                                                                                |
| -------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `aud`          | Identifies the **Google Cloud resource** that IAP protects, so a token issued for another Google Cloud project can’t satisfy your policy. Supports wildcards (`*`), which lets a single rule cover more than one backend service in the same project. *Example*: `/projects/123456789012/global/backendServices/9876543210987654321` *Wildcard example*: `/projects/123456789012/global/backendServices/*` |
| `custom_claim` | Matches on **any additional claim** in the token. You define both the claim name and its expected value, and you can add multiple custom claim match rules to a single Trust Provider. IAP signs a fixed set of claims, so `hd`, the hosted domain, is the practical option here. *Example*: Key `hd`, Value `example.com`                                                                                 |
| `email`        | Matches the **email address** of the user that IAP authenticated. Unlike `sub`, this value has no namespace prefix. *Example*: `user@example.com`                                                                                                                                                                                                                                                          |
| `iss`          | Identifies who **issued** the token. For IAP assertions this is always `https://cloud.google.com/iap`, and Aembit validates it for you. *Example*: `https://cloud.google.com/iap`                                                                                                                                                                                                                          |
| `sub`          | Identifies **who** the token is about. Google sets this to a unique, stable identifier for the user, so it keeps identifying the same person even if their email address changes. Unlike `email`, this value carries a namespace prefix, so match on the full string rather than a bare user ID. *Example*: `accounts.google.com:1234567890123456789`                                                      |

> **Keep wildcards narrow**
>
> Every wildcard widens the set of tokens Aembit accepts. Keep the project number and as much of the resource path explicit as you can, so an `aud` rule still rejects tokens that IAP issued for a different Google Cloud project.

## Configuration

You configure the GCP IAP JWT Trust Provider the same way you configure any Trust Provider, by choosing the type and adding your match rules. Select **Google Cloud Identity-Aware Proxy (IAP) JWT** from the Trust Provider list, then add a match rule for each claim you want to verify. To add and associate the Trust Provider with an Access Policy, see [How to add a Trust Provider](add-trust-provider.md).

The one value you must supply is the **IAP Audience**. Aembit pins the issuer and resolves Google’s signing keys automatically, so there are no keys to upload and no discovery endpoint to enter.

Before Aembit can validate a token, you must configure IAP in Google Cloud to protect your backend and forward requests to it.

### Find your IAP audience

The audience identifies the Google Cloud resource that IAP protects, so its format depends on where your workload runs:

| Where your workload runs | Audience format                                                         |
| ------------------------ | ----------------------------------------------------------------------- |
| App Engine               | `/projects/<PROJECT_NUMBER>/apps/<PROJECT_ID>`                          |
| Compute Engine and GKE   | `/projects/<PROJECT_NUMBER>/global/backendServices/<SERVICE_ID>`        |
| Cloud Run                | `/projects/<PROJECT_NUMBER>/locations/<REGION>/services/<SERVICE_NAME>` |

For a workload behind a global backend service, retrieve `<PROJECT_NUMBER>` and `<SERVICE_ID>` with the `gcloud` CLI.

To get your `<PROJECT_NUMBER>`, run:

```shell
gcloud projects describe <PROJECT_ID>
```

Copy the value of `projectNumber` from the output:

```yaml
createTime: '2016-10-13T16:44:28.170Z'
lifecycleState: ACTIVE
name: project_name
parent:
  id: '433637338589'
  type: organization
projectId: <PROJECT_ID>
projectNumber: <PROJECT_NUMBER>
```

To get your backend `<SERVICE_ID>`, run:

```shell
gcloud compute backend-services describe <SERVICE_NAME> --project=<PROJECT_ID> --global
```

Copy the value of `id` from the output:

```yaml
affinityCookieTtlSec: 0
backends:
- balancingMode: UTILIZATION
  capacityScaler: 1.0
  group: https://www.googleapis.com/compute/v1/projects/project_name/regions/us-central1/instanceGroups/my-group
connectionDraining:
  drainingTimeoutSec: 0
creationTimestamp: '2017-04-03T14:01:35.687-07:00'
description: ''
enableCDN: false
fingerprint: zaOnO4k56Cw=
healthChecks:
- https://www.googleapis.com/compute/v1/projects/project_name/global/httpsHealthChecks/my-hc
id: <SERVICE_ID>
kind: compute#backendService
loadBalancingScheme: EXTERNAL
name: my-service
port: 8443
portName: https
protocol: HTTPS
selfLink: https://www.googleapis.com/compute/v1/projects/project_name/global/backendServices/my-service
sessionAffinity: NONE
timeoutSec: 3610
```

For the Google Cloud console equivalent of the preceding commands, see [Verifying the IAP JWT assertion](https://cloud.google.com/iap/docs/signed-headers-howto).

### Terraform

You can manage the GCP IAP JWT Trust Provider with the Aembit Terraform provider using the `aembit_trust_provider` resource. For the resource schema and arguments, see the [Aembit provider documentation](https://registry.terraform.io/providers/Aembit/aembit/latest/docs) in the Terraform Registry.

## Limitations

* **No Google Group support.** IAP assertions don’t carry group membership, so you can’t write group-based match rules from the token alone. Match on `email`, `sub`, or the `hd` hosted domain instead.
* **Minimal claim set.** IAP signs only a small, fixed set of claims, so profile attributes that your identity provider holds aren’t available to match on.
* **Audience required.** You must supply the IAP audience. Aembit doesn’t infer it from the token.
* **No static key upload.** This provider resolves signing keys dynamically from Google and doesn’t accept an uploaded JWKS or public key.

## Troubleshooting

If Aembit can’t resolve the signing key for a token, it denies the request rather than allow access it can’t verify. Keys that Aembit has already cached continue to validate tokens during a temporary Google outage.

If Aembit denies a request, check the following:

1. Open the Access Authorization Event for the request. The Trust Provider entry includes a `reason` that explains why attestation failed. See [Authorization Failure](../../audit-report/access-authorization-events.md#authorization-failure) for the values.

2. Compare the audience Aembit recorded against the one you configured, including the leading slash. The event records the actual audience string from the token. An audience mismatch can come from a project ID used instead of a project number, or a wildcard that’s too narrow.

3. Confirm your workload forwards the `x-goog-iap-jwt-assertion` header. This Trust Provider only verifies that token.
