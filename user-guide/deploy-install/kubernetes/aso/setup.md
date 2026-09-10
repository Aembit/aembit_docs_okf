---
type: how-to
title: "Set up Secrets Operator"
description: "Install and configure Aembit Secrets Operator in your Kubernetes cluster"
resource: https://docs.aembit.io/user-guide/deploy-install/kubernetes/aso/setup/
interface: web-ui
tags: ["aso", "kubernetes", "deploy-install"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Set up Secrets Operator

This page walks you through installing Aembit Secrets Operator in your Kubernetes cluster and configuring it to retrieve credentials from the Aembit platform.

Secrets Operator supports two authentication paths to the Aembit platform:

* **OIDC symmetric key (HS256)**: Secrets Operator signs tokens with a shared secret you generate and configure. Choose this when you need custom claims (for example, a store number), non-Kubernetes identity, or want to keep an existing prototype configuration. The following steps use this path.
* **Kubernetes Service Account**: Secrets Operator authenticates using its in-cluster ServiceAccount token, validated by your cluster’s OIDC endpoint. No signing key required. Choose this for Kubernetes-native identity and tighter match rules (namespace, pod name, ServiceAccount name). See [Kubernetes Service Account authentication](#kubernetes-service-account-authentication).

## Prerequisites

Before you begin, ensure you have:

* A Kubernetes cluster (verified on Amazon EKS and K3s)
* `kubectl` configured for your target cluster
* Helm v3.x installed

If you plan to use CrowdStrike Access Conditions, you also need:

* CrowdStrike Falcon agent installed on cluster nodes
* CrowdStrike Agent ID written to `/etc/aembit/crowdstrike-aid` on each node

## Create the OIDC signing key

Secrets Operator authenticates to Aembit using OIDC tokens signed with a symmetric key. Generate a cryptographically random key and store it as a Kubernetes Secret. You need the base64-encoded value from this Secret when you configure the Trust Provider in your Aembit tenant.

1. Generate the key and create the Secret:

   ```shell
   # Generate a 32-byte random key and create the Secret
   openssl rand 32 | kubectl create secret generic aembit-oidc-signing-key \
     --from-file=key=/dev/stdin \
     --namespace <your-namespace>
   ```

2. Retrieve the base64-encoded key value for use in your Trust Provider:

   ```shell
   kubectl get secret aembit-oidc-signing-key \
     --namespace <your-namespace> \
     -o jsonpath='{.data.key}'
   ```

   Copy this value—you paste it into the Trust Provider’s symmetric key field in the next section.

> **Avoid double base64 encoding**
>
> Kubernetes automatically base64-encodes Secret data. When you configure the Trust Provider, paste the base64-encoded value from the preceding command directly into the symmetric key field. Don’t base64-encode the value yourself before pasting—doing so results in a double-encoded key that causes authentication failures.

> **Key requirements**
>
> The signing key must be at least 32 bytes of cryptographically random data. Secrets Operator rejects keys shorter than 32 bytes (per RFC 7518 §3.2) when generating tokens.

> **Note**
>
> If you change the OIDC signing key Secret after Secrets Operator is running, you must restart the Secrets Operator pod for Secrets Operator to pick up the new key.

## Create the claims ConfigMap

Create a ConfigMap that defines the OIDC claims included in Secrets Operator’s identity tokens. The `iss` (issuer) and `sub` (subject) claims control how the Trust Provider identifies Secrets Operator. You can also add claims for per-cluster access policies.

```shell
kubectl create configmap aembit-oidc-claims \
  --namespace <your-namespace> \
  --from-literal=iss=https://aembit-secrets-operator.example.com \
  --from-literal=sub=aembit-secrets-operator
```

> **Why create this ConfigMap**
>
> Without a claims ConfigMap, Secrets Operator infers a default `iss` claim that’s specific to the cluster and difficult to obtain. To find it, you would need to install the Helm chart and check Secrets Operator logs. Creating the ConfigMap upfront gives you explicit control over the `iss` and `sub` values, which makes configuring Trust Provider match rules straightforward.

You reference this ConfigMap in the `AembitEdgeApiClient` custom resource when you [create custom resources](#create-custom-resources).

## Configure your Aembit tenant

Set up the required Aembit resources to define the access policy for Secrets Operator. Secrets Operator authenticates using OIDC tokens signed with a symmetric key, so the Trust Provider and Client Workload both use the **OIDC ID Token** type.

1. Log in to your [Aembit tenant](https://app.aembit.io) and create a **Client Workload**:

   * Set the identification method to **OIDC ID Token**.
   * Configure the subject match value to match the `sub` value you set in the [claims ConfigMap](#create-the-claims-configmap) (for example, `aembit-secrets-operator`).

2. Create a **Trust Provider**:

   * Set the type to **OIDC ID Token**.
   * Select **Symmetric Key** as the attestation method.
   * Paste the base64-encoded key value you copied in [Create the OIDC signing key](#create-the-oidc-signing-key).
   * Configure match rules for `iss` (issuer) and `sub` (subject) to match the values you set in the [claims ConfigMap](#create-the-claims-configmap).
   * Note the **Edge SDK Client ID**—you need this for the `clientId` field in the `AembitEdgeApiClient` Custom Resource Definition (CRD).

3. Create a **Server Workload** pointing to your HashiCorp Vault instance:

   * Configure the host and port to match the values you plan to set in the `AembitSecretRefreshSchedule` CRD (`server.host` and `server.port`).

4. Create a **Credential Provider**:

   * Select **Vault Client Token** as the credential type.
   * Configure the JWT subject, custom claims, and Vault authentication details.
   * See [Configure a HashiCorp Vault Client Token Credential Provider](../../../access-policies/credential-providers/vault-client-token.md) for detailed instructions.

5. (Optional) Create an **Access Condition** for [CrowdStrike](../../../access-policies/access-conditions/crowdstrike.md) device posture validation. If enabled, Secrets Operator passes host attestation data (hostname, serial number, CrowdStrike Agent ID) with every authentication request. See [Prepare host attestations](#prepare-host-attestations-optional) to configure the host attestation file.

6. Create an **Access Policy** linking the Client Workload, Trust Provider, Server Workload, Credential Provider, and any Access Conditions.

7. Note the following values from your tenant configuration. You need these for the CRD configuration:

   | Value                           | Where to find it              | Used in                                     |
   | ------------------------------- | ----------------------------- | ------------------------------------------- |
   | Tenant ID                       | Tenant settings               | `AembitEdgeApiClient` `spec.tenantId`       |
   | Edge SDK Client ID              | Trust Provider details page   | `AembitEdgeApiClient` `spec.clientId`       |
   | Resource Set ID (if applicable) | Resource Set settings         | `AembitEdgeApiClient` `spec.resourceSetId`  |
   | Server Workload host and port   | Server Workload configuration | `AembitSecretRefreshSchedule` `spec.server` |

## Prepare host attestations (optional)

If your Access Policy includes Access Conditions that validate device posture (for example, CrowdStrike device identity), prepare a host attestations file on each cluster node before deploying Secrets Operator.

Secrets Operator reads host attestation data from a file mounted into the pod via a `hostPath` volume. The file must be available at the configured path (default: `/run/aembit/host_attestations.json`) on each node.

The file must contain a JSON object that satisfies the `host` field of the [Edge API authentication schema](../../../../dev-guide/api/edge/overview.md#visual-tree-diagram). For example:

**/run/aembit/host\_attestations.json**

```json
{
  "hostname": "node-01.example.com",
  "domainName": "example.com",
  "systemSerialNumber": "ABC123DEF456",
  "sensors": {
    "crowdStrike": {
      "agentId": "a1b2c3d4-e5f6-7890-abcd-ef1234567890"
    }
  }
}
```

For more details on configuring host attestations, see the [host attestations reference](reference.md#host-attestations).

## Install the Helm chart

> **Use a dedicated namespace**
>
> Install Secrets Operator in its own namespace. The Helm chart creates a ServiceAccount with access to all Secrets in the namespace, and the OIDC signing key is readable by any ServiceAccount in the same namespace. A dedicated namespace limits the blast radius of these permissions.
>
> If you install the chart more than once in a cluster, install each instance into its own namespace. The chart creates one `ClusterRole` and `ClusterRoleBinding` per installation; these names include the install namespace, so the same Helm release name works across multiple namespaces without conflicts.
>
> For details, see [Security model](overview.md#security-model).

1. Add the Aembit Helm repository:

   ```shell
   helm repo add aembit https://helm.aembit.io
   helm repo update
   ```

   > **Repo name conflict**
   >
   > If you see this error:
   >
   > ```plaintext
   > Error: repository name (aembit) already exists, please specify a different name
   > ```
   >
   > You already have a local Helm repo registered under that name. Run `helm repo list` to see your existing repos and choose a different alias for this command.

2. (Optional) Create a `values.yaml` file to override default chart values. For the complete list of configurable values, see the [Helm chart values reference](helm-values.md).

3. Install Secrets Operator:

   ```shell
   helm install aembit-secrets-operator aembit/aembit-secrets-operator \
     --namespace aembit-system \
     --create-namespace
   ```

   If you created a `values.yaml`, add `--values values.yaml` to the command.

4. Verify the Secrets Operator pod is running:

   ```shell
   kubectl get pods --namespace aembit-system
   ```

   Expected output:

   ```plaintext
   NAME                                    READY   STATUS    RESTARTS   AGE
   aembit-secrets-operator-<id>            1/1     Running   0          30s
   ```

## Create custom resources

Create the custom resources that tell Secrets Operator what credentials to manage.

> **Installation order**
>
> Installing the Helm chart and creating custom resources are independent operations—you can do either first. This guide recommends installing the Helm chart first because it registers the Custom Resource Definitions (CRDs) that Kubernetes needs to accept the custom resources below.

1. Save the following manifest as `aembit-connection.yaml`, replacing the placeholder values with the tenant ID and Edge SDK Client ID from your Aembit tenant:

   **aembit-connection.yaml**

   ```yaml
   apiVersion: aembit.io/v1
   kind: AembitEdgeApiClient
   metadata:
     name: aembit-connection
     namespace: aembit-system
   spec:
     tenantId: "<your-tenant-id>"
     clientId: "<your-edge-sdk-client-id>"
     # resourceSetId: "<your-resource-set-id>"  # Optional
     attestations:
       oidc:
         source: generated
         signingSecretRef:
           name: aembit-oidc-signing-key
           secretField: key
         claimsConfigMapRef:
           name: aembit-oidc-claims
   ```

2. Apply the manifest to create the `AembitEdgeApiClient` resource:

   ```shell
   kubectl apply -f aembit-connection.yaml
   ```

3. Save the following manifest as `vault-token-schedule.yaml`, replacing the `server.host` and `server.port` values with your Vault instance details. Create one `AembitSecretRefreshSchedule` for each credential you need managed:

   **vault-token-schedule.yaml**

   ```yaml
   apiVersion: aembit.io/v1
   kind: AembitSecretRefreshSchedule
   metadata:
     name: vault-token-schedule
     namespace: aembit-system
   spec:
     aembitEdgeApiClientRef:
       name: aembit-connection
     targetSecretName: vault-token
     # credentialType: OAuthToken  # Optional; defaults to OAuthToken. See the Configuration Reference for other types.
     server:
       host: vault.example.com
       port: 8200
     # refreshInterval: "4h"  # Optional: omit to use the credential's natural lifetime
   ```

4. Apply the manifest to create the schedule:

   ```shell
   kubectl apply -f vault-token-schedule.yaml
   ```

> **Retrieving a non-Vault credential**
>
> This walkthrough retrieves a HashiCorp Vault client token, which uses the default `credentialType` (`OAuthToken`). To retrieve an API key, username/password, AWS STS, or Google federation credential instead, set `spec.credentialType` to match your Credential Provider. See [Credential types and Secret data keys](reference.md#credential-types-and-secret-data-keys) for the full list and the Secret data keys each type produces.
>
> To deliver an X.509-SVID certificate instead of a fetched credential, see [Deliver X.509-SVID certificates with Secrets Operator](x509-svid.md), which covers in-cluster key generation and the TLS Secret workflow.

## Verify the installation

Confirm Secrets Operator is managing credentials successfully.

1. Check the status of your custom resources:

   ```shell
   kubectl get aembitedgeapiclient --namespace aembit-system
   kubectl get aembitsecretrefreshschedule --namespace aembit-system
   ```

2. Inspect the credential schedule status for details:

   ```shell
   kubectl describe aembitsecretrefreshschedule vault-token-schedule --namespace aembit-system
   ```

   Look for `phase: Ready` and a recent `lastSyncTime` indicating successful credential retrieval. See the [status fields reference](reference.md#aembitsecretrefreshschedule-status) for details.

3. Verify that the target Secret exists:

   ```shell
   kubectl get secret vault-token --namespace aembit-system
   ```

   The Secret should exist and contain the credential data.

## Kubernetes Service Account authentication

Kubernetes Service Account authentication is an alternative to the OIDC symmetric key path above. Secrets Operator authenticates using its in-cluster ServiceAccount token — no signing key or claims ConfigMap required.

Skip the [Create the OIDC signing key](#create-the-oidc-signing-key) and [Create the claims ConfigMap](#create-the-claims-configmap) sections. All other steps (install the Helm chart, create custom resources, verify) are the same.

### Configure your Aembit tenant for Kubernetes Service Account authentication

In the [Configure your Aembit tenant](#configure-your-aembit-tenant) steps, make these changes:

1. Create a **Client Workload**:

   * Set the identification method to **Kubernetes Service Account**.
   * Configure match rules to identify the Secrets Operator pod (for example, namespace and ServiceAccount name).

2. Create a **Trust Provider**:

   * Set the type to **Kubernetes Service Account**.
   * Provide your cluster’s OIDC issuer URL. See [Kubernetes Service Account Trust Provider](../../../access-policies/trust-providers/kubernetes-service-account-trust-provider.md) for instructions by cluster type (EKS and K3s).
   * Configure match rules (namespace, pod name, ServiceAccount name, or subject).
   * Note the **Edge SDK Client ID** — you need this for the `clientId` field.

3. Complete the remaining tenant steps (Server Workload, Credential Provider, Access Policy) the same way.

### Create the AembitEdgeApiClient for Kubernetes Service Account authentication

Use `kubernetesServiceAccount: {}` instead of `oidc:` in the `attestations` field:

**aembit-connection.yaml**

```yaml
apiVersion: aembit.io/v1
kind: AembitEdgeApiClient
metadata:
  name: aembit-connection
  namespace: aembit-system
spec:
  tenantId: "<your-tenant-id>"
  clientId: "<your-edge-sdk-client-id>"
  attestations:
    kubernetesServiceAccount: {}          # uses default SA token path
    # kubernetesServiceAccount:           # or use a custom projected-volume path:
    #   tokenPath: /var/run/secrets/custom/token
```

Apply the manifest and then create the `AembitSecretRefreshSchedule` as described in [Create custom resources](#create-custom-resources). The credential retrieval flow is identical regardless of auth path.

## Next steps

* [Configuration Reference](reference.md): CRD specifications, environment variables, and host attestations.
* [Helm chart values](helm-values.md): All configurable Helm chart values.
* [Vault Client Token Credential Provider](../../../access-policies/credential-providers/vault-client-token.md): Configure the Aembit Credential Provider for HashiCorp Vault.
