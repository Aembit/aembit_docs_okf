---
type: reference
title: "About TLS Decrypt"
description: "Overview of how TLS Decrypt works"
resource: https://docs.aembit.io/user-guide/deploy-install/advanced-options/tls-decrypt/
interface: web-ui
tags: ["tls-decrypt", "advanced-option", "deploy-install"]
timestamp: 2026-09-08T23:32:41-07:00
---

# About TLS Decrypt

TLS Decrypt allows the Aembit Agent Proxy to decrypt and manage encrypted traffic between your Client and Server Workloads, enabling Workload IAM functionality.

To configure TLS Decrypt, see [Configure TLS Decrypt](configure-tls-decrypt.md).

One of the most important aspects of TLS decryption is the way in which you manage keys and certificates. Aembit has implemented the following set of security measures to make sure TLS decryption is secure in your Aembit environment:

* Aembit stores private keys for certificates used in TLS decryption in the Agent Proxy memory only, which never persists.

* The private key that Aembit uses for the TLS Decrypt CA is securely stored and kept in Aembit Cloud.

* The default lifetime for a TLS decryption certificate is 1 day.

* TLS certificates are only generated for the target host. Wildcards are explicitly **not** used.

* The certificate hostname can only match the hostnames that are in your Server Workloads.

* A certificate is only issued if the certificate meets the requirements of the Access Policy, which includes Client Workload and Server Workload identification, Trust Provider attestation, successful validation of conditional access checks.

* Each Aembit Tenant has a unique Root CA, making sure TLS decryption certificates issued by one tenant aren’t trusted by Client Workloads configured to trust the Root CA of a different tenant.

  > **Caution**
  >
  > Since Aembit issues each tenant its own Root CA, Aembit recommends setting up separate tenants for environments with distinct security boundaries.
  >
  > By configuring separate tenants, each environment remains securely isolated. This prevents potential risks where an actor uses a certificate issued in one environment (with lower safeguards) to attack another environment with stricter safeguards.

## Example workflow

When a Client Workload first attempts to establish a connection to a Server Workload, Agent Proxy intercepts the connection, generates a key pair and Certificate Signing Request (CSR), and then requests a certificate for TLS decryption from Aembit Cloud. This certificate is then cached and reused for subsequent connections until a [configurable percentage of its lifetime](configure-tls-decrypt.md#change-your-leaf-certificate-lifetime) has elapsed, optimizing performance while maintaining security.

Once Aembit Cloud evaluates the request and authorizes the Client Workload to access the Server Workload, Aembit Cloud issues a certificate the Agent Proxy can use to decrypt TLS and permit the Client Workload to access the Server Workload.

## Decryption scope

Aembit Agent Proxy only decrypts connections when it evaluates and matches the associated Access Policy, and the Server Workload for this Access Policy has the TLS checkbox enabled.

Because of these restrictions, the Agent Proxy only decrypts the connection when it:

* Identifies the Client Workload

* Identifies the Server Workload

* Finds the associated Access Policy

* Attests the Client Workload

* Conditional Access checks pass

* Server Workload has the TLS checkbox enabled

If any of these conditions aren’t met, Aembit leaves the connection intact and doesn’t decrypt it.

## Standalone CA for TLS Decrypt

Instead of using your Aembit Tenant’s CA, you have the option to define and use your own Standalone CA.

See [About Standalone CA for TLS Decrypt](about-tls-decrypt-standalone-ca.md) to learn more.

To set up a Standalone CA, see [How to configure a Standalone CA](configure-tls-decrypt-standalone-ca.md).
