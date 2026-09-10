---
type: how-to
title: "Deliver X.509-SVID certificates with Secrets Operator"
description: "Use Aembit Secrets Operator to request an X.509-SVID certificate and deliver it to a Kubernetes TLS Secret"
resource: https://docs.aembit.io/user-guide/deploy-install/kubernetes/aso/x509-svid/
interface: web-ui
tags: ["aso", "kubernetes", "deploy-install"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Deliver X.509-SVID certificates with Secrets Operator

Aembit Secrets Operator (ASO) can deliver X.509-SVID certificates to your Kubernetes workloads. When a schedule uses the X.509-SVID Credential Provider, Secrets Operator generates a private key inside the cluster and obtains a signed certificate from Aembit. It writes both into a standard Kubernetes `kubernetes.io/tls` Secret that your workload consumes for mTLS.

Secrets Operator generates the private key in-cluster, and the key never leaves the cluster. Secrets Operator sends only a certificate signing request to Aembit for signing, and refreshes the certificate automatically before it expires.

## How certificate delivery works

![Secrets Operator X.509-SVID certificate flow: in-cluster key generation, certificate signing by Aembit, and delivery to a Kubernetes TLS Secret](https://docs.aembit.io/d2/docs/user-guide/deploy-install/kubernetes/aso/x509-svid-0.svg)

1. **Generate the key in-cluster**: Secrets Operator generates a private key and a certificate signing request inside the cluster. The private key stays in the cluster and is never transmitted to Aembit.

2. **Sign the certificate**: Secrets Operator requests a certificate from Aembit for the Access Policy that applies to it. Aembit signs an X.509-SVID certificate and returns the signed certificate chain. The chain holds the leaf certificate and any intermediates. The Aembit root Certificate Authority (CA) isn’t included, so the Server Workload the certificate authenticates to must already trust the issuing Aembit Standalone CA.

3. **Write to the Secret**: Secrets Operator writes the signed chain to `tls.crt` and the in-cluster private key to `tls.key` in a `kubernetes.io/tls` Secret. Secrets Operator creates the Secret if it doesn’t exist.

4. **Consume the certificate**: Your workload reads the certificate and key from the Secret using standard Kubernetes mechanisms, such as a volume mount or environment variables.

5. **Refresh automatically**: Secrets Operator generates a new key and requests a new certificate before the current one expires, then updates the Secret. By default, it refreshes at 80% of the certificate’s lifetime.

## Prerequisites

Before you begin, make sure you have:

* **Secrets Operator installed** in your cluster with a working `AembitEdgeApiClient`. See [Set up Secrets Operator](setup.md) to install and configure it.
* **An X.509-SVID Credential Provider** in your Aembit tenant. See [Create an X.509-SVID Credential Provider](../../../access-policies/credential-providers/spiffe-x509-svid.md).
* **An Access Policy** that binds the Secrets Operator Client Workload to the X.509-SVID Credential Provider.

## Configure the certificate identity in the tenant

You configure the certificate’s identity and constraints on the Credential Provider, not on the Secrets Operator schedule. Configure the following on the X.509-SVID Credential Provider in your Aembit tenant:

* The **SPIFFE ID** that Aembit embeds as the certificate’s URI Subject Alternative Name.
* The certificate **lifetime**.
* The **Extended Key Usage (EKU)** values that control which side of the TLS handshake the certificate can authenticate.

For the meaning of each option, see [About the X.509-SVID Credential Provider](../../../access-policies/credential-providers/about-spiffe-x509-svid.md). The Secrets Operator schedule doesn’t set any of these values—it only selects the Credential Provider type.

## Create the certificate schedule

Create an `AembitSecretRefreshSchedule` that sets `credentialType` to `X509Svid`. This is the only field that differs from a token schedule.

1. Save the following manifest as `x509-svid-schedule.yaml`, replacing the `server.host` and `server.port` values with the details of the Server Workload the certificate authenticates to:

   **x509-svid-schedule.yaml**

   ```yaml
   apiVersion: aembit.io/v1
   kind: AembitSecretRefreshSchedule
   metadata:
     name: x509-svid-schedule
     namespace: aembit-system
   spec:
     aembitEdgeApiClientRef:
       name: aembit-connection
     targetSecretName: workload-tls
     credentialType: X509Svid
     server:
       host: api.example.com
       port: 443
     # refreshInterval: "4h"  # Optional: omit to refresh at 80% of the certificate lifetime
   ```

2. Apply the manifest to create the schedule:

   ```shell
   kubectl apply -f x509-svid-schedule.yaml
   ```

For the full field reference, see [Credential types and Secret data keys](reference.md#credential-types-and-secret-data-keys).

> **The Secret is created in the schedule’s namespace**
>
> Secrets Operator writes the target Secret to the same namespace as the `AembitSecretRefreshSchedule`. Because Kubernetes Secrets can’t be mounted across namespaces, create the schedule and its `AembitEdgeApiClient` in the same namespace as the workload that consumes the certificate.

> **Switching `credentialType` recreates the Secret**
>
> A Kubernetes Secret’s `type` is immutable. Switching an existing schedule’s `credentialType` to or from `X509Svid` changes the target Secret’s type between a generic Secret and a `kubernetes.io/tls` Secret, so Secrets Operator deletes and recreates the Secret. Any workload mounting the Secret sees it briefly disappear and reappear.

## Verify the certificate

Confirm Secrets Operator issued the certificate and wrote it to the Secret.

1. Check the schedule status:

   ```shell
   kubectl describe aembitsecretrefreshschedule x509-svid-schedule --namespace aembit-system
   ```

   A healthy schedule reports the `Ready` phase and a populated `credentialExpiresAt`.

2. Confirm the Secret exists and has the `kubernetes.io/tls` type with `tls.crt` and `tls.key` keys:

   ```shell
   kubectl get secret workload-tls --namespace aembit-system \
     --output jsonpath='{.type}{"\n"}{range $k, $v := .data}{$k}{"\n"}{end}'
   ```

   ```plaintext
   kubernetes.io/tls
   tls.crt
   tls.key
   ```

## Consume the certificate from a workload

Mount the Secret into your workload the same way you consume any `kubernetes.io/tls` Secret. Add it as a volume in your workload’s pod spec so your application reads the certificate and key from files. The following excerpt shows the relevant `spec` fields of a Pod (use the same fields under `spec.template.spec` in a Deployment):

**Pod spec excerpt**

```yaml
spec:
  containers:
    - name: app
      volumeMounts:
        - name: workload-tls
          mountPath: /etc/tls
          readOnly: true
  volumes:
    - name: workload-tls
      secret:
        secretName: workload-tls
```

Your application reads the certificate from `/etc/tls/tls.crt` and the private key from `/etc/tls/tls.key`. Secrets Operator refreshes the certificate in place. Mount the Secret as a volume rather than injecting it as environment variables, so your workload picks up rotated material without a restart.

> **The Secret contains no trust bundle**
>
> The Secret holds only `tls.crt` and `tls.key`—the workload’s own certificate and private key. It doesn’t include a `ca.crt` key. If your workload also needs to verify the certificate of the server it connects to, provide that server’s CA through a ConfigMap or other trust source.

## Limitations

* **No certificate revocation**: Aembit doesn’t publish Certificate Revocation Lists or Online Certificate Status Protocol responders for X.509-SVID certificates. Short certificate lifetimes and automatic rotation are the primary controls.
* **No service mesh integration**: Secrets Operator delivers certificates to Kubernetes Secrets only. It doesn’t integrate with Envoy Secret Discovery Service (SDS) or Istio sidecar certificate provisioning. For transparent mesh-style mTLS, use [Agent Proxy](../../../access-policies/credential-providers/about-spiffe-x509-svid.md) instead.
* **Chain excludes the root CA**: The delivered chain contains the leaf certificate and any intermediates, but not the Aembit root CA. The Server Workload must already trust the issuing Aembit Standalone CA.

## Next steps

* [About the X.509-SVID Credential Provider](../../../access-policies/credential-providers/about-spiffe-x509-svid.md): How X.509-SVID certificates work and how to choose between consumers.
* [Enable mTLS on a Server Workload](../../../access-policies/server-workloads/enable-mtls.md): Configure the Server Workload that authenticates the certificate.
* [Secrets Operator Configuration Reference](reference.md): CRD fields, status fields, and Secret data keys.
