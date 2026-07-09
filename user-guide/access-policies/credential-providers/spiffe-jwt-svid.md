---
type: how-to
title: "Create a JWT-SVID Token Credential Provider"
description: "How to create a JWT-SVID Token Credential Provider"
resource: https://docs.aembit.io/user-guide/access-policies/credential-providers/spiffe-jwt-svid/
interface: web-ui
tags: [credential-provider, access-policy]
timestamp: 2026-06-26T15:17:49-07:00
type_inferred: true
---

# Create a JWT-SVID Token Credential Provider

The [JWT-SVID Token Credential Provider](about-spiffe-jwt-svid.md) enables secure identity token generation that complies with SPIFFE (Secure Production Identity Framework for Everyone) standards.

This credential provider is similar to the [OIDC ID Token Credential Provider](oidc-id-token.md), but enforces SPIFFE-specific formatting requirements for the subject claim.

You can configure the following options for your JWT-SVID Token Credential Provider:

* Subject (SPIFFE ID) configuration with literal or dynamic values.
* Automatic issuer URL generation based on your tenant.
* Audience configuration for target system validation.
* Token lifetime settings (default 1 hour).
* Signing algorithms (RS256 and ES256).
* Custom claims for enhanced workload context.

## Create a JWT-SVID Token Credential Provider

[Section titled “Create a JWT-SVID Token Credential Provider”](#create-a-jwt-svid-token-credential-provider)

To create a JWT-SVID Token Credential Provider, follow these steps:

1. Log into your Aembit Tenant, and go to **Credential Providers** in the left sidebar.

2. (Optional) In the top right corner, select the [Resource Set](../../administration/resource-sets/overview.md) that you want this Credential Provider to reside.

3. Click **+ New**, which displays the Credential Provider pop out menu.

4. Enter a **Name** and optional **Description**.

5. Under **Credential Type**, select **JWT-SVID Token**, revealing more fields.

6. Fill out the remaining fields:

   * **Subject** - Enter the SPIFFE ID that you want as the subject claim in the JWT-SVID.

     SPIFFE Format Required

     The subject **must** use SPIFFE formatting:

     ```text
     spiffe://<trust-domain>/<workload-path>
     ```

     The subject always starts with `spiffe://` followed by your `<trust-domain>` and `<workload-path>`. For example: `spiffe://example.com/workload/api-service`

     Choose how to specify the subject:

     * **Literal** - Enter a fixed SPIFFE ID (for example, `spiffe://example.com/workload/api-service`)

     * **Dynamic** - Use variables to generate SPIFFE IDs at runtime. Use the syntax `${expression}` to create dynamic values.

       For example:

       * `spiffe://your-domain/ns/${namespace}/sa/${serviceaccount}` for Kubernetes
       * `spiffe://your-domain/aws/account/${account}/role/${role}` for AWS

   * **Issuer** - Aembit automatically generates this value based on your tenant information. The issuer URL identifies who created and signed the token.

   * **Audience** - Enter the identifiers that the receiving service expects in the `aud` claim. This can be a single string value (for example, `my-service.example.com`)

     The audience must match what your SPIFFE-aware target system expects for validation.

   * **Lifetime** - Specify how long (in minutes) your JWT-SVIDs remain valid after issuance.

     * Default: 15 minutes
     * Shorter lifetimes increase security
     * SPIFFE recommends tokens expire within 1 hour

   * **Algorithm Type** - Select the signing algorithm for your JWT-SVIDs:

     * **RSASSA-PKCS1-v1\_5 using SHA-256** (RS256) - Default, widely compatible
     * **ECDSA using P-256 and SHA-256** (ES256) - Recommended for SPIFFE-compliant systems

7. (Optional) For **Custom Claims**, click **+ New Claim**.

   Custom claims provide additional context about the workload identity. Common SPIFFE JWT-SVID custom claims include:

   * `namespace` - Kubernetes namespace
   * `service_account` - Kubernetes service account name
   * `aws_account` - AWS account ID
   * `environment` - Deployment environment (production, staging, etc.)
   * `region` - Geographic or cloud region
   * `cluster` - Kubernetes cluster name

   Then fill out the following:

   1. Enter **Claim Name** (for example: `namespace`, `cluster`, `environment`).

   2. For **Value** enter the value based on which type you choose:

      * **Literal** - Enter the exact string value to include in the token
      * **Dynamic** - Enter an expression using the syntax `${expression}` to extract values from workload identity

   Dynamic claims examples for SPIFFE

   * Extract Kubernetes namespace: `${oidc.identityToken.decode.payload.namespace}`
   * Extract service account: `${oidc.identityToken.decode.payload.service_account}`
   * Extract AWS account: `${aws.account}`
   * Environment variable: `${os.environment.CLUSTER_NAME}`
   * Combine values: `${namespace}_${environment}`

   For detailed information on dynamic claims syntax and examples, see [Dynamic Claims for OIDC and JWT-SVID Tokens](advanced-options/dynamic-claims-oidc.md).

8. (Optional) Repeat the preceding step for each additional Claim.

9. Click **Save**.

## Verify your JWT-SVID Token Credential Provider

[Section titled “Verify your JWT-SVID Token Credential Provider”](#verify-your-jwt-svid-token-credential-provider)

To verify a JWT-SVID token is retrievable and formatted correctly, follow these steps:

1. In your Aembit Tenant, go to **Credential Providers** in the left sidebar.

2. Select the JWT-SVID Token from the list of Credential Providers that you want to verify.

   This reveals the Credential Provider pop out menu.

3. Click **Verify** at the top.

   ![Verify JWT-SVID Token Credential Provider](https://docs.aembit.io/_astro/oidc-id-token-verify.LuUCHbDJ_Z1OCYlP.webp)

4. When successful, Aembit posts a green notification that says “**Verified successfully**”.

   The verification confirms:

   * Subject follows SPIFFE format (starts with `spiffe://`)
   * JWT header type set to “JWT”
   * Token includes the configured claims
   * Correct scope set for the credential provider
   * Token signing works with selected algorithm

   If the verification isn’t successful, double check your configuration to make sure all the values are correct, then try again. Common issues include:

   * Invalid SPIFFE ID format (must start with `spiffe://`)
   * Missing or invalid trust domain

## JWKS endpoint for verification

[Section titled “JWKS endpoint for verification”](#jwks-endpoint-for-verification)

SPIFFE-aware systems can verify JWT-SVIDs issued by Aembit using the public JWKS endpoint:

```shell
https://<your-tenantId>.id.useast2.aembit.io/.well-known/openid-configuration/jwks
```

This endpoint provides:

* Public keys for signature verification
* Support for both ES256 and RS256 algorithms
* Automatic key rotation management
* Standards-compliant JWKS format

## Integration with SPIFFE-aware systems

[Section titled “Integration with SPIFFE-aware systems”](#integration-with-spiffe-aware-systems)

Once configured, your JWT-SVID Token Credential Provider can authenticate workloads to:

* **Service Meshes** - Istio, Consul, Linkerd, and other SPIFFE-compliant service meshes
* **SPIFFE Libraries** - Applications using SPIFFE SDK libraries for token validation
* **Zero Trust Platforms** - Security platforms that validate SPIFFE identities
* **Custom Services** - Any service configured to validate JWT-SVIDs against Aembit’s JWKS endpoint

For more information about SPIFFE standards and implementation, see:

* [SPIFFE JWT-SVID Specification](https://spiffe.io/docs/latest/keyless/)
* [How to Construct SPIFFE IDs](https://www.spirl.com/blog/how-to-construct-spiffe-ids/)
