---
type: explanation
title: "AWS Application Load Balancer JWT Trust Provider"
description: "How to configure an AWS Application Load Balancer JWT Trust Provider to validate signed tokens forwarded by an AWS Application Load Balancer"
resource: https://docs.aembit.io/user-guide/access-policies/trust-providers/aws-alb-jwt-trust-provider/
tags: ["trust-provider", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# AWS Application Load Balancer JWT Trust Provider

The AWS Application Load Balancer JWT Trust Provider validates the signed JSON Web Token (JWT) that an Application Load Balancer (ALB) forwards to your workloads. Use this Trust Provider instead of the OIDC ID Token Trust Provider when your workloads sit behind an ALB that authenticates users.

After the ALB authenticates the user session with OpenID Connect (OIDC), it adds a signed `x-amzn-oidc-data` header to each request. This Trust Provider verifies that token, extracts the user identity, and evaluates your Access Policy before authorizing access.

Aembit resolves and rotates the AWS signing keys for you, so your workload only forwards the token.

## How the AWS ALB JWT Trust Provider works

Aembit resolves the signing key at validation time rather than from a static, preconfigured key set. During authentication, your workload and Aembit Edge perform a clear sequence of actions.

1. Your workload extracts the `x-amzn-oidc-data` header that the ALB adds to the request, then presents that token to Aembit Edge to prove the user’s identity.

2. Aembit reads the AWS region and key ID (`kid`) from the token. Aembit validates the region before it contacts AWS, so it only requests keys from legitimate AWS endpoints.

3. Aembit retrieves the public signing key for that `kid` from AWS and validates the token’s signature against it.

4. If the signature is valid, Aembit compares the token’s claims against the match rules you configured, including the issuer, audience, subject, and any custom claims.

5. If the signature and all claims are valid, Aembit authorizes the request and applies the relevant Access Policies. If any check fails, Aembit denies the request.

![How Aembit validates an AWS ALB JWT: the workload forwards the ALB token, and Aembit resolves the regional signing key, validates the token, and evaluates the Access Policy](https://docs.aembit.io/d2/docs/user-guide/access-policies/trust-providers/aws-alb-jwt-trust-provider-0.svg)

## Match rules

You authorize access by matching on the claims in the ALB token. Each match rule pairs an **Attribute** with the **Value** you expect that claim to have. The following table describes the attributes available for the AWS ALB JWT Trust Provider:

| Attribute      | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| -------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `aud`          | Identifies who the token is **for**. It specifies the intended recipient, which prevents another service from reusing a token created for one purpose. *Example*: `aembit-prod-api-access`                                                                                                                                                                                                                                                                                                 |
| `iss`          | Identifies who **issued** the token. Aembit also uses the AWS region in this claim to locate the correct regional signing key. *Example*: `https://elb.us-east-2.amazonaws.com`                                                                                                                                                                                                                                                                                                            |
| `sub`          | Identifies **what or who** the token is about. It’s a unique, case-sensitive string that represents the specific principal the token authenticates. *Example*: `user-id-xyz-123`                                                                                                                                                                                                                                                                                                           |
| `email`        | Matches the user’s **email address**, a common identifier for the person the ALB authenticated. Your identity provider must forward the `email` claim through the ALB. *Example*: `user@example.com`                                                                                                                                                                                                                                                                                       |
| `custom_claim` | Matches on **any additional claim** in the token, including the attributes your identity provider forwards through the ALB (for example, an Okta group or department). When you select `custom_claim`, the form replaces the Value field with **Key** and **Value** fields: enter the claim name as the Key and the expected claim value as the Value. You can add more than one `custom_claim` rule to a single Trust Provider. *Example*: Key `department`, Value `platform-engineering` |

## Configuration

You configure this Trust Provider the same way you configure any Trust Provider, by choosing the type and adding your match rules. Select **AWS Application Load Balancer JWT** from the Trust Provider list, then add a match rule for each claim you want to verify. To add and associate the Trust Provider with an Access Policy, see [How to add a Trust Provider](add-trust-provider.md).

This Trust Provider has no type-specific configuration fields. Aembit derives the AWS region from the token, so you don’t configure a region or upload any signing keys.

Before Aembit can validate a token, you must configure your ALB to authenticate users with OIDC and forward the claims you want to match on. See the AWS documentation for [authenticating users with an Application Load Balancer](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/listener-authenticate-users.html#user-claims-preprocess).

### Terraform

You can manage this Trust Provider with the Aembit Terraform provider using the `aembit_trust_provider` resource. For the resource schema and arguments, see the [Aembit provider documentation](https://registry.terraform.io/providers/Aembit/aembit/latest/docs) in the Terraform Registry.

## Limitations

* **Agent Controller isn’t supported yet.** This Trust Provider works with Aembit Edge, not with Agent Controller.
* **No static key upload.** This provider resolves signing keys dynamically from AWS and doesn’t accept an uploaded JWKS or public key.

## Troubleshooting

If Aembit can’t resolve the signing key for a token, it denies the request rather than allow access it can’t verify. Keys that Aembit has already cached continue to validate tokens during a temporary AWS outage.

If Aembit denies a request, check the following:

1. Open the Access Authorization Event for the request. The Trust Provider entry includes a `reason` that explains why attestation failed. See [Authorization Failure](../../audit-report/access-authorization-events.md#authorization-failure) for the values.

2. If you recently rotated signing keys, wait approximately five minutes and retry. Aembit doesn’t re-attempt a key ID it failed to resolve until then, so it can deny a token that uses a new key during that short window.

3. If you were previously using the OIDC ID Token Trust Provider and saw an `IDX12709` error, switch to this provider. ALB tokens require the AWS ALB JWT Trust Provider.
