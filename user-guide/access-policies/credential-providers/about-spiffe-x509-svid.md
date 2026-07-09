---
type: how-to
title: "About the X.509-SVID Credential Provider"
description: "This page describes the X.509-SVID Credential Provider and how it works"
resource: https://docs.aembit.io/user-guide/access-policies/credential-providers/about-spiffe-x509-svid/
interface: web-ui
tags: [credential-provider, access-policy]
timestamp: 2026-06-03T15:12:48-04:00
type_inferred: true
---

# About the X.509-SVID Credential Provider

The X.509-SVID Credential Provider issues short-lived X.509 certificates that comply with the SPIFFE**SPIFFE**: Secure Production Identity Framework For Everyone (SPIFFE) is an open standard for workload identity that provides cryptographically verifiable identities to services without relying on shared secrets.[Learn more(opens in new tab)](https://spiffe.io/docs/latest/spiffe-about/overview/) standard. Each certificate embeds a workload’s SPIFFE ID as a URI Subject Alternative Name (SAN) for use with mTLS**mTLS**: Mutual Transport Layer Security. A TLS handshake in which both the client and server present certificates and validate each other's identity before any application traffic flows. mTLS is commonly used for workload-to-workload authentication where both parties must prove identity at the transport layer.[Learn more](../server-workloads/enable-mtls.md) in environments where SPIFFE-aware Server Workloads authenticate Client Workloads against their SPIFFE ID.

The X.509-SVID Credential Provider supports:

* SPIFFE-compliant certificates with the workload’s SPIFFE ID as a URI SAN.

* Literal or dynamic Subject and SPIFFE ID configuration.

* Signing through an Aembit Standalone Certificate Authority (CA).

* Configurable certificate lifetime.

* Configurable Extended Key Usage (EKU). TLS client authentication (`id-kp-clientAuth`) by default, with optional TLS server authentication (`id-kp-serverAuth`).

* Two retrieval workflows:

  * **Agent Proxy mTLS** - Aembit generates the private key in memory and rotates the certificate automatically. Best for long-running services that can host a sidecar or host-level proxy.
  * **Aembit CLI** - You supply the private key on disk and re-run the CLI before each expiration. Best for serverless workloads, CI/CD runners, and BYO-key workflows where existing key material must be reused.

See [Create an X.509-SVID Credential Provider](spiffe-x509-svid.md) to create one. To configure the Server Workload that authenticates the certificate, see [Enable mTLS on a Server Workload](../server-workloads/enable-mtls.md).

## Common use cases

[Section titled “Common use cases”](#common-use-cases)

* **Workload-to-workload mTLS** - Mutually authenticated TLS between Client and Server Workloads with both sides validating certificates at the transport layer.
* **Integration with SPIFFE-aware services** - Connecting to Server Workloads that validate SPIFFE-compliant X.509-SVIDs, without running your own SPIFFE infrastructure.
* **Zero Trust architecture** - Cryptographically verifiable workload identities for environments that require identity proof on every connection.
* **Managed identity issuance** - Aembit-managed certificate issuance, rotation, and trust bundle**trust bundle**: A collection of Certificate Authority (CA) certificates that a SPIFFE-aware workload uses to validate X.509-SVIDs and other SPIFFE identities presented during authentication.[Learn more(opens in new tab)](https://spiffe.io/docs/latest/spiffe-about/spiffe-concepts/#trust-bundle) distribution as an alternative to running a SPIFFE control plane.
* **Disk-based credential delivery for serverless and CI/CD** - Retrieve a signed certificate from the same Credential Provider through the Aembit CLI in environments where running a sidecar isn’t practical.

## When to choose Agent Proxy or Aembit CLI

[Section titled “When to choose Agent Proxy or Aembit CLI”](#when-to-choose-agent-proxy-or-aembit-cli)

Aembit provides two ways to consume certificates from an X.509-SVID Credential Provider. Both paths use the same Credential Provider configuration and the same Aembit Cloud signing flow. They differ in where the private key lives, how rotation works, and what kind of workload they fit.

| Aspect              | Agent Proxy                                               | Aembit CLI                                                                      |
| ------------------- | --------------------------------------------------------- | ------------------------------------------------------------------------------- |
| **Private key**     | Aembit generates the key in memory; never written to disk | You supply the key on disk; the CLI never transmits it to Aembit Cloud          |
| **Rotation**        | Automatic at regular intervals                            | One-shot per invocation; you re-run the CLI before expiration                   |
| **App integration** | Transparent—Agent Proxy intercepts outbound TLS           | The CLI returns the certificate chain in `CLIENT_CERT_CHAIN` for your TLS stack |
| **Deployment**      | Sidecar or host-level proxy                               | Single CLI binary, on-demand                                                    |
| **Best fit**        | Long-running services with stable network footprint       | Short-lived workloads and externally managed key custody                        |

Choose Agent Proxy when:

* The workload runs somewhere a sidecar or host-level proxy can live.
* You want zero application code changes. Agent Proxy handles outbound mTLS transparently.
* You need automatic certificate rotation with no external orchestration.

Choose the Aembit CLI when:

* The workload can’t host a sidecar.
* You have existing key material that must be reused.
* You’re bootstrapping credentials into a process that consumes them from environment variables.
* Your scheduling layer re-runs the CLI before certificate expiration.

The two paths aren’t mutually exclusive. A single Aembit policy can be served by Agent Proxy for long-running services and by the Aembit CLI for short-lived jobs against the same Credential Provider.

CLI workflow requires `CLIENT_CERT_CHAIN`

When you retrieve an X.509-SVID certificate through the Aembit CLI, you must include `CLIENT_CERT_CHAIN` in the `--credential-names` flag. See [`aembit credentials get`](../../../cli-guide/reference/credentials-get.md#--credential-names) for details.

## How the X.509-SVID Credential Provider works

[Section titled “How the X.509-SVID Credential Provider works”](#how-the-x509-svid-credential-provider-works)

1. **Certificate issuance** - Aembit Cloud signs each SPIFFE-compliant X.509 certificate using the Aembit Standalone CA assigned to the Resource Set or Client Workload that uses the Credential Provider. Each certificate carries the Client Workload’s SPIFFE ID as a URI Subject Alternative Name and is suitable for use with mTLS.

2. **SPIFFE ID configuration** - Aembit Cloud sets the URI SAN using your configured SPIFFE ID:

   * **Literal** - Uses a fixed SPIFFE ID value that you provide (must start with `spiffe://`).
   * **Dynamic** - Derives the SPIFFE ID from workload attributes using template expressions. For details, see [Dynamic Claims for OIDC ID Token, JWT-SVID Token, and X.509-SVID Credential Providers](advanced-options/dynamic-claims-oidc.md).

3. **Certificate consumption** - How the certificate reaches your TLS stack depends on which consumer you use:

   * **Agent Proxy** presents the certificate during the TLS handshake to a SPIFFE-aware Server Workload, which validates it against its configured trust bundle.
   * **Aembit CLI** returns the signed certificate chain in the `CLIENT_CERT_CHAIN` shell variable so the caller can use it directly in its own TLS client. The chain holds the leaf certificate first, followed by any intermediates. The Aembit root CA isn’t included, so the Server Workload your TLS client connects to must already trust the issuing Aembit Standalone CA. Most TLS libraries accept the chain written to a temporary PEM file alongside the private key path you passed to `--client-tls-private-key`. See [`--client-tls-private-key`](../../../cli-guide/reference/credentials-get.md#--client-tls-private-key) for the flag reference and worked example.

Private key custody

Agent Proxy and Aembit CLI handle the certificate’s private key differently:

* **Agent Proxy** generates the key pair in memory on each rotation. The key is never written to disk and is never transmitted to Aembit Cloud.
* **Aembit CLI** uses a key you supply through the `--client-tls-private-key` flag. Aembit CLI never transmits the private key to Aembit Cloud.

## Configuration options

[Section titled “Configuration options”](#configuration-options)

The following sections describe the configuration options at a conceptual level. See [Create an X.509-SVID Credential Provider](spiffe-x509-svid.md) for configuration steps.

### Certificate subject

[Section titled “Certificate subject”](#certificate-subject)

The Subject is the X.509 Distinguished Name (DN) carried in the certificate. SPIFFE-aware Server Workloads authenticate via the URI SAN, not the Subject DN, so the Subject DN doesn’t participate in identity verification. It functions as optional descriptive metadata—useful for audit logs, compliance frameworks that require a populated DN, or non-SPIFFE-aware Server Workloads that authorize clients by reading the Subject DN.

### SPIFFE ID

[Section titled “SPIFFE ID”](#spiffe-id)

The SPIFFE ID is the workload’s identity. Aembit embeds it as the URI Subject Alternative Name on the issued certificate, and SPIFFE-aware Server Workloads authenticate the Client Workload by matching this value against their configured trust policy.

Configure a literal SPIFFE ID when a single workload uses the Credential Provider, or a dynamic expression when one Credential Provider issues distinct identities to multiple workloads based on attestation attributes (for example, Kubernetes namespace and service account).

### Certificate lifetime

[Section titled “Certificate lifetime”](#certificate-lifetime)

Certificates are short lived by design. The Lifetime field governs how long an issued certificate is valid.

When Agent Proxy consumes the certificate, it refreshes the certificate automatically at regular intervals.

When the Aembit CLI consumes the certificate, each invocation returns one chain for one lifetime. The caller re-runs `aembit credentials get` before the certificate expires using whichever mechanism fits the environment, such as a cron job, a scheduled CI step, or a process supervisor hook. For workloads that need continuous certificate rotation, use Agent Proxy mTLS instead.

### TLS handshake roles

[Section titled “TLS handshake roles”](#tls-handshake-roles)

Every certificate carries an **Extended Key Usage (EKU)** value that controls which side of the TLS handshake it can authenticate.

By default, Aembit Cloud issues the certificate with `id-kp-clientAuth`, which authenticates the certificate holder when it initiates outbound TLS—the role Agent Proxy plays when presenting the certificate on behalf of a Client Workload calling an upstream service.

Include `id-kp-serverAuth` in the Credential Provider’s **Extended Key Usage** field when the same workload also accepts inbound TLS connections and needs to present this certificate to the connecting party.

### Certificate constraints

[Section titled “Certificate constraints”](#certificate-constraints)

All issued certificates carry the following constraints:

* The certificate can’t sign other certificates.
* Subject Alternative Name is restricted to URI SAN only. No DNS or IP SANs.
* Aembit Cloud sets the SPIFFE ID URI SAN during signing; the consumer (Agent Proxy or Aembit CLI) cannot modify it in the request.

## Limitations

[Section titled “Limitations”](#limitations)

* **Cert chain only — no root CA**: Aembit returns the leaf certificate and any intermediates, but not the root CA.
* **Certificate revocation** — Aembit does not publish Certificate Revocation Lists (CRLs) or Online Certificate Status Protocol (OCSP) responders for X.509-SVID certificates. Short certificate lifetimes and automatic rotation are the primary controls.
* **External CA chaining** — You can’t chain X.509-SVID issuance to a non-Aembit-managed root CA. Aembit Cloud always signs X.509-SVID certificates with an Aembit Standalone CA; you cannot substitute a different root CA.
* **Service mesh integration** — Direct integration with service mesh certificate provisioning (such as Istio) is not supported.

## Additional resources

[Section titled “Additional resources”](#additional-resources)

* [Create an X.509-SVID Credential Provider](spiffe-x509-svid.md)
* [Enable mTLS on a Server Workload](../server-workloads/enable-mtls.md)
* [`aembit credentials get --client-tls-private-key`](../../../cli-guide/reference/credentials-get.md#--client-tls-private-key)
* [SPIFFE concepts](https://spiffe.io/docs/latest/spiffe-about/spiffe-concepts/)
* [SPIFFE X.509-SVID specification](https://github.com/spiffe/spiffe/blob/main/standards/X509-SVID.md)
* [SPIFFE Helper](https://github.com/spiffe/spiffe-helper)
