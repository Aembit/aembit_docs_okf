---
type: reference
title: "Aembit Secrets Operator"
description: "Overview of Aembit Secrets Operator (ASO) for Kubernetes credential management"
resource: https://docs.aembit.io/user-guide/deploy-install/kubernetes/aso/
interface: web-ui
tags: ["aso", "kubernetes", "deploy-install"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Aembit Secrets Operator

Aembit Secrets Operator (ASO) is a Kubernetes operator that automates credential management by authenticating to the Aembit platform and synchronizing credentials into Kubernetes Secrets. Applications consume Secrets Operator–managed secrets the same way they consume any other Kubernetes Secret.

## When to use Secrets Operator

Secrets Operator provides an alternative deployment model to the [Aembit Edge Helm chart](../kubernetes.md) (Agent Proxy + Controller + Injector). Choose the model that fits your environment:

|                         | Secrets Operator                                                                             | Agent Proxy (Helm chart)                            |
| ----------------------- | -------------------------------------------------------------------------------------------- | --------------------------------------------------- |
| **How it works**        | Writes credentials to Kubernetes Secrets                                                     | Intercepts traffic via sidecar proxy                |
| **Application changes** | None—apps read standard K8s Secrets                                                          | None—sidecar handles auth transparently             |
| **Best for**            | Edge/offline environments, cert-manager integration, K3s clusters                            | Always-connected environments, HTTP/gRPC workloads  |
| **Offline resilience**  | Credentials persist through multi-day outages                                                | Requires active connection for credential injection |
| **Credential types**    | Tokens, API keys, username/password, AWS STS, Google federation, and X.509-SVID certificates | All supported credential types                      |

## Architecture

Secrets Operator authenticates to the Aembit platform via the [Edge API](../../../../dev-guide/api/edge/overview.md), retrieves credentials based on your configured Access Policies, and writes them to Kubernetes Secrets that your applications consume directly.

![Secrets Operator authentication and credential retrieval sequence: OIDC token exchange with Aembit Cloud, credential write to Kubernetes Secret, application consumption, and proactive renewal](https://docs.aembit.io/d2/docs/user-guide/deploy-install/kubernetes/aso/index-0.svg)

### Authentication and credential flow

1. **Authenticate**: Secrets Operator sends an OIDC identity token and device posture data to the Aembit Edge API. Aembit validates Secrets Operator’s identity against your configured Trust Provider and Access Conditions (including CrowdStrike device posture).

2. **Retrieve credentials**: After successful authentication, Aembit returns the requested credentials based on your Access Policy configuration.

3. **Write to Secret**: Secrets Operator writes the credential to a Kubernetes Secret in the target namespace.

4. **Application consumes**: Your application reads the Secret using standard Kubernetes mechanisms, such as environment variables, volume mounts, or application-specific Secret references.

5. **Proactive renewal**: Secrets Operator monitors credential expiration and renews before expiry, ensuring a valid credential is always available. The refresh interval is configurable per credential.

## Custom resource definitions

Secrets Operator uses two Custom Resource Definitions (CRDs) to manage its configuration:

### AembitEdgeApiClient

Defines the connection to the Aembit platform, including OIDC authentication and host attestation configuration. You typically create one per cluster.

```yaml
apiVersion: aembit.io/v1
kind: AembitEdgeApiClient
metadata:
  name: aembit-connection
spec:
  tenantId: "<your-tenant-id>"
  clientId: "<your-edge-sdk-client-id>"
  attestations:
    oidc:
      source: generated
      signingSecretRef:
        name: aembit-oidc-signing-key
        secretField: key
```

For optional fields—including Kubernetes Service Account authentication and host attestation passthrough—see the [Configuration Reference](reference.md#aembitedgeapiclient-spec-fields).

### AembitSecretRefreshSchedule

Defines which credential to retrieve, where to store it, and how often to refresh. Create one for each credential you need managed.

```yaml
apiVersion: aembit.io/v1
kind: AembitSecretRefreshSchedule
metadata:
  name: vault-token-schedule
spec:
  aembitEdgeApiClientRef:
    name: aembit-connection
  targetSecretName: vault-token
  server:
    host: vault.example.com
    port: 8200
```

To override the default refresh schedule, set `refreshInterval` in the spec. For all available fields, see the [Configuration Reference](reference.md#aembitsecretrefreshschedule-spec-fields).

## Key features

* **Automatic credential synchronization**: Secrets Operator fetches credentials from Aembit and writes them to Kubernetes Secrets without manual intervention.
* **Flexible authentication**: Secrets Operator supports two authentication paths: OIDC symmetric key signing (HS256) with configurable subject and custom claims, or Kubernetes Service Account token authentication using the cluster’s native OIDC endpoint. Both paths can run simultaneously for phased migrations or mixed Access Policy requirements.
* **Host attestations**: Passes host-level device posture data (for example, CrowdStrike Agent ID, hostname, serial number) with every authentication request, satisfying Access Conditions that validate device identity.
* **Proactive credential rotation**: Secrets Operator renews credentials before expiry. Failed rotations trigger automatic retry with backoff.
* **Configurable token TTL**: Token lifetimes are fully configurable, supporting TTLs of 7+ days for environments with extended network outage windows.
* **Certificate delivery**: Secrets Operator delivers X.509-SVID certificates to `kubernetes.io/tls` Secrets, generating the private key in-cluster and refreshing the certificate automatically. See [Deliver X.509-SVID certificates with Secrets Operator](x509-svid.md).
* **Kubernetes-native**: Managed entirely through CRDs and Helm. Compatible with GitOps workflows (ArgoCD, FluxCD).

## Security model

Secrets Operator follows a defense-in-depth approach appropriate for physically exposed environments:

* **Non-root container**: Runs as UID 65532 with a read-only filesystem and all capabilities dropped.
* **Namespace-scoped RBAC**: Secrets Operator only accesses resources in its own namespace by default.
* **Namespace isolation**: The Helm chart creates a ServiceAccount with read/write access to all Secrets in the namespace. Secrets Operator reads the OIDC signing key from a Kubernetes Secret in the namespace, so any workload or principal with `get secrets` RBAC in that namespace can also read it. Install Secrets Operator in a dedicated namespace to minimize the set of workloads that share secret-read access with it.
* **Credential protection**: Credentials are never written to logs. Debug output shows only the last 4 characters of sensitive values.
* **Host attestation passthrough**: Secrets Operator reads device posture data from the node via a read-only `hostPath` mount. No persistent privileged containers run.
* **Identity verification**: Aembit validates all claimed device attributes against the configured backend (for example, CrowdStrike). A compromised node can’t impersonate another node’s hardware identity.
* **TLS 1.2+**: All communication with Aembit uses TLS 1.2 or later with forward secrecy.
* **Multiple installations**: You can install the Helm chart more than once in a cluster, including across multiple namespaces using the same Helm release name. The chart creates `ClusterRole` and `ClusterRoleBinding` resources named after the release and install namespace, preventing naming conflicts.

## Current limitations

* **Namespace-scoped only**: Secrets Operator manages secrets within its own namespace. Cluster-scoped operation for platform teams managing multiple namespaces isn’t supported yet.
* **No Prometheus metrics**: Secrets Operator doesn’t expose a metrics endpoint.
* **No cross-cluster synchronization**: Each cluster requires its own independent Secrets Operator instance. Cross-cluster credential sync isn’t architecturally supported.
* **X.509-SVID certificate constraints**: Certificate delivery doesn’t support revocation or service mesh integration. Aembit publishes no Certificate Revocation Lists or Online Certificate Status Protocol responders, and Secrets Operator doesn’t integrate with Envoy Secret Discovery Service or Istio sidecars. For transparent mesh-style mTLS, use [Agent Proxy](../../../access-policies/credential-providers/about-spiffe-x509-svid.md) instead.

## Next steps

* [Set up Secrets Operator](setup.md): Install and configure Secrets Operator in your cluster.
* [Configuration Reference](reference.md): CRD specifications, environment variables, and host attestations.
* [Helm chart values](helm-values.md): All configurable Helm chart values.
