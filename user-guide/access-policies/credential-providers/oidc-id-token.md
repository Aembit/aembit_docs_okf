---
type: how-to
title: "Create an OIDC ID Token Credential Provider"
description: "How to create an OIDC ID Token Credential Provider"
resource: https://docs.aembit.io/user-guide/access-policies/credential-providers/oidc-id-token/
interface: web-ui
tags: ["credential-provider", "access-policy"]
timestamp: 2026-09-22T11:51:31-07:00
---

# Create an OIDC ID Token Credential Provider

The [OIDC ID Token Credential Provider](about-oidc-id-token.md) enables secure identity token generation and exchange with third-party services.

You can configure the following options for your OIDC ID Token Credential Provider:

* custom claims configuration.
* flexible signing algorithms (ES256 and RS256).
* support for Workload Identity Federation (WIF) solutions such as AWS Security Token Service (STS), Google Cloud Platform (GCP) WIF, Azure WIF, Vault, and more.

## Create an OIDC ID Token Credential Provider

To create an OIDC ID Token Credential Provider, follow these steps:

1. Log into your Aembit Tenant, and go to **Credential Providers** in the left sidebar.

2. (Optional) In the top right corner, select the [Resource Set](../../administration/resource-sets/overview.md) that you want this Credential Provider to reside.

3. Click **+ New**, which displays the Credential Provider pop out menu.

4. Enter a **Name** and optional **Description**.

5. Under **Credential Type**, select **OIDC ID Token**, revealing more fields.

6. Fill out the remaining fields:

   * **Subject** - Enter the unique identifier for the workload receiving the token.

     Choose how Aembit determines the subject claim in your OIDC tokens:

     * **Dynamic** - Aembit determines the subject at runtime based on the calling workload’s identity. You can use expressions like `${oidc.identityToken.decode.payload.user_login}` to extract values from incoming OIDC tokens.
     * **Literal** - Aembit uses a fixed value that as the subject for all tokens

   * **Issuer** - The issuer URL identifies who created and signed the token.

     This value should match what your relying party expects. Aembit automatically generates this value based on your tenant information.

   * **Lifetime** - Specify how long (in minutes; default: 15) your OIDC tokens remain valid after issuance. Match your security requirements and target system expectations:

     * Shorter lifetimes (minutes to hours) increase security
     * Longer lifetimes reduce token refresh frequency

   * **Signing Algorithm Type** - Select the algorithm Aembit uses to sign your OIDC tokens:

     * **RSASSA-PKCS1-v1\_5 using SHA-256** - RS256 Signature with SHA-256 (widely supported)
     * **ECDSA using P-256 and SHA-256** - ES256 signature with P-256 curve and SHA-256

   * **Audience** - Enter the URI or identifier of the service or API that validates this token. This should match what your target identity broker or service expects.

7. (Optional) To enable refresh token support, toggle **Enable Refresh Token Support** to on.

   When enabled, the [MCP Authorization Server](../../deploy-install/mcp-authorization-server/concepts-mcp-auth-server.md#token-refresh) can return refresh tokens alongside access tokens during OAuth token requests. Refresh tokens are single-use, and each exchange returns a new refresh token. This option only applies to MCP Authorization Server flows.

   Set the **Absolute Token Lifetime** to control how long refresh tokens remain valid for exchanging for new access tokens after initial issuance. This value must be greater than the **Lifetime** value. The absolute token lifetime doesn’t reset with each refresh token exchange. Once it expires, the client must complete a new authorization flow.

8. (Optional) For **Custom Claims**, click **New Claim**.

   For a list of common custom claims, see [Common OIDC claims](about-oidc-id-token.md#common-oidc-claims).

   Then fill out the following:

   1. Enter **Claim Name** (for example: `groups`, `email`, `role`, `environment`).

   2. For **Value** enter the value based on which type you choose:

      * **Literal** - Enter the exact string value to include in the token
      * **Dynamic** - Enter an expression using the syntax `${expression}` or extract claims from OIDC tokens

   Dynamic claims examples

   * Extract user email from OIDC token: `${oidc.identityToken.decode.payload.user_email}`
   * Extract user login: `${oidc.identityToken.decode.payload.user_login}`
   * Environment variable: `${os.environment.K8S_POD_NAME}`
   * Combine values: `${oidc.identityToken.decode.payload.user_login}_custom_suffix`

   For detailed information on dynamic claims syntax and examples, see [Dynamic Claims](advanced-options/dynamic-claims.md).

9. (Optional) Repeat the preceding step for each additional Claim.

   ![Completed OIDC ID Token Credential Provider example](https://docs.aembit.io/_astro/oidc-id-token.3fk7iYxY_ZsxzVj.webp)

10. Click **Save**.

## Verify your OIDC ID Token Credential Provider

To verify an OIDC ID Token is retrievable from the identity provider you configured, follow these steps:

1. In your Aembit Tenant, go to **Credential Providers** in the left sidebar menu.

2. Select the OIDC ID Token from the list of Credential Providers that you want to verify.

   This reveals the Credential Provider pop out menu.

3. Click **Verify** at the top.

   ![Verify OIDC ID Token Credential Provider](https://docs.aembit.io/_astro/oidc-id-token-verify.LuUCHbDJ_Z1OCYlP.webp)

4. When successful, Aembit posts a green notification that says “**Verified successfully**.”

   If the verification isn’t successful, double check your configuration to make sure all the values are correct, then try again.

## Related

**Compatible trust providers**

* [AWS Metadata Service](../trust-providers/aws-metadata-service-trust-provider.md)
* [AWS Role](../trust-providers/aws-role-trust-provider.md)
* [Azure Instance Metadata Service](../trust-providers/azure-metadata-service-trust-provider.md)
* [Certificate Signed Attestation](../trust-providers/certificate-signed-attestation-trust-provider.md)
* [GCP Identity Token](../trust-providers/gcp-identity-token-trust-provider.md)
* [GitHub Action ID Token](../trust-providers/github-trust-provider.md)
* [GitLab Job ID Token](../trust-providers/gitlab-trust-provider.md)
* [Kerberos](../trust-providers/kerberos-trust-provider.md)
* [Kubernetes Service Account](../trust-providers/kubernetes-service-account-trust-provider.md)
* [OIDC ID Token](../trust-providers/oidc-id-token-trust-provider.md)
* [SAMLv2 Response](../trust-providers/saml-response-trust-provider.md)
* [Terraform Cloud Identity Token](../trust-providers/terraform-cloud-identity-token-trust-provider.md)

**Server workloads that accept this credential**

* [Okta](../server-workloads/guides/okta.md)
