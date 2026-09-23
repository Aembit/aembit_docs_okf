---
type: how-to
title: "Create an X.509-SVID Credential Provider"
description: "How to create an X.509-SVID Credential Provider"
resource: https://docs.aembit.io/user-guide/access-policies/credential-providers/spiffe-x509-svid/
interface: web-ui
tags: ["credential-provider", "access-policy"]
timestamp: 2026-09-22T11:51:31-07:00
---

# Create an X.509-SVID Credential Provider

Create an X.509-SVID Credential Provider in your Aembit Tenant. See [About the X.509-SVID Credential Provider](about-spiffe-x509-svid.md) for a conceptual overview. That overview covers how to choose between consuming the certificate through Agent Proxy, Aembit CLI, or Aembit Secrets Operator.

## Prerequisites

Before you create an X.509-SVID Credential Provider, confirm the following:

* One of the following consumers is in place:

  * **Agent Proxy v1.31 or later** to consume the certificate during outbound mTLS to a Server Workload.
  * **Aembit CLI v1.32 or later** to retrieve the certificate on demand using `--client-tls-private-key`. See [`aembit credentials get`](../../../dev-guide/cli/reference/credentials-get.md#--client-tls-private-key) for details.
  * **Aembit Secrets Operator** to deliver the certificate to a Kubernetes TLS Secret.

* The destination Server Workload supports validating SPIFFE-compliant X.509-SVID certificates and uses the **mTLS Authentication** method. See [Enable mTLS on a Server Workload](../server-workloads/enable-mtls.md).

* A **Standalone CA** is available to sign X.509-SVID certificates. Standalone CAs are a paid feature that your Aembit representative must enable on your Aembit Tenant. See [Configure a Standalone CA](../../deploy-install/advanced-options/tls-decrypt/configure-tls-decrypt-standalone-ca.md) to create and assign one.

## Create an X.509-SVID Credential Provider

To create an X.509-SVID Credential Provider, follow these steps:

1. Log into your Aembit Tenant, and click **Credential Providers**.

2. (Optional) Select the [Resource Set](../../administration/resource-sets/overview.md) that you want this Credential Provider to reside in.

3. Click **+ New**.

4. Enter a **Name** and optional **Description**.

5. Under **Credential Type**, select **X.509-SVID Certificate**, revealing more fields.

6. Fill out the remaining fields:

   * **Subject** - (Optional) The X.509 certificate Subject Distinguished Name (DN).

     SPIFFE-aware Server Workloads identify the Client Workload from the SPIFFE ID in the URI Subject Alternative Name (SAN), not from the Subject DN. Populate it only in these cases. A non-SPIFFE-aware Server Workload reads the Subject DN to authorize clients, a compliance framework requires a populated DN, or you want descriptive certificate metadata for auditing.

     Choose how to specify the Subject:

     * **Literal** - Enter a fixed DN (for example, `CN=api-service,O=example.com,C=US`).
     * **Dynamic** - Use template expressions with the syntax `${expression}` to resolve DN components at issuance time from the Client Workload’s attestation.

   * **SPIFFE ID** - Enter the SPIFFE ID that Aembit embeds as the URI Subject Alternative Name on the issued certificate.

     > **SPIFFE format required**
     >
     > The SPIFFE ID must use SPIFFE formatting:
     >
     > ```text
     > spiffe://<trust-domain>/<workload-path>
     > ```
     >
     > The value always starts with `spiffe://` followed by your `<trust-domain>` and `<workload-path>`. For example: `spiffe://example.com/workload/api-service`

     Choose how to specify the SPIFFE ID:

     * **Literal** - Enter a fixed SPIFFE ID (for example, `spiffe://example.com/workload/api-service`).

     * **Dynamic** - Use template expressions that resolve at issuance time using values from the Client Workload’s attestation. Use the syntax `${expression}`.

       For example:

       * `spiffe://your-domain/ns/${oidc.identityToken.decode.payload.namespace}/sa/${oidc.identityToken.decode.payload.service_account}` for Kubernetes
       * `spiffe://your-domain/aws/account/${aws.account}/role/${aws.role}` for AWS

     For detailed dynamic-expression syntax, see [Dynamic Claims](advanced-options/dynamic-claims.md).

   * **Lifetime** - Enter the certificate lifetime in minutes (default 15).

     When Agent Proxy consumes the certificate, it refreshes the certificate automatically before expiration. When the Aembit CLI consumes the certificate, each `aembit credentials get` invocation returns one certificate for one lifetime; you’re responsible for re-running the CLI before the certificate expires.

   * **Key Usage** - Digital Signature is the only Key Usage that the X.509-SVID Credential Provider supports.

   * **Extended Key Usage**—Select one or both values to include on the issued certificate:

     * **`id-kp-clientAuth`** (default)—The certificate authenticates the holder when it initiates a TLS handshake. This is the correct choice for outbound mTLS from Agent Proxy.
     * **`id-kp-serverAuth`** - The certificate also authenticates the holder when it accepts a TLS handshake. Include this only when the same workload also accepts inbound TLS connections and presents this certificate to the connecting party.

   * **Standalone Certificate Authority** - (Optional) Select a specific Standalone CA to sign leaf certificates issued by this Credential Provider.

     When you select a Standalone CA here, Aembit uses it for every X.509-SVID certificate issued by this Credential Provider. This overrides the Standalone CA otherwise associated with the Resource Set or Client Workload. Ensure your Server Workload environment trusts the selected Standalone CA using configuration appropriate to that environment.

     If you leave this field empty, Aembit uses the Standalone CA assigned to the Resource Set (or to the specific Client Workload).

   * **Tags** - (Optional) Add tags to organize and filter the Credential Provider in your Aembit Tenant.

   ![Filled-out X.509-SVID Credential Provider form](https://docs.aembit.io/_astro/x509-svid-cp-form.DlS-eQyS_Z1xQu0H.webp)

7. Click **Save**.

## Use the Credential Provider in an Access Policy

After you save the Credential Provider, attach it to an Access Policy that links a Client Workload to a Server Workload configured for mTLS Authentication:

1. Click **Access Policies**.

2. Create a new Access Policy or select an existing one.

3. Add the Client Workload, Server Workload (with **mTLS Authentication** configured), Trust Provider, and the new X.509-SVID Credential Provider.

4. Click **Save**.

## Next steps

How you use the Credential Provider depends on which consumer your workload uses.

**Agent Proxy** - Requires no additional configuration. At runtime, Agent Proxy resolves the policy, generates a key pair, and submits a Certificate Signing Request (CSR) to Aembit Cloud. It then uses the returned X.509-SVID certificate for the mTLS handshake to the Server Workload.

**Aembit CLI** - Invoke `aembit credentials get` with [`--client-tls-private-key`](../../../dev-guide/cli/reference/credentials-get.md#--client-tls-private-key) and include `CLIENT_CERT_CHAIN` in `--credential-names`.

**Aembit Secrets Operator** - Create an `AembitSecretRefreshSchedule` with `credentialType: X509Svid` to deliver the certificate to a Kubernetes TLS Secret. See [Deliver X.509-SVID certificates with Secrets Operator](../../deploy-install/kubernetes/aso/x509-svid.md).
