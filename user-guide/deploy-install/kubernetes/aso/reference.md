---
type: reference
title: "Secrets Operator Configuration Reference"
description: "CRD specifications, environment variables, and host attestation reference for Aembit Secrets Operator"
resource: https://docs.aembit.io/user-guide/deploy-install/kubernetes/aso/reference/
interface: web-ui
tags: ["aso", "kubernetes", "deploy-install"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Secrets Operator Configuration Reference

This page provides the Custom Resource Definition (CRD) specifications, environment variables, and host attestation reference for Aembit Secrets Operator (ASO). For Helm chart configuration, see [Helm chart values](helm-values.md).

## Custom resource definitions

Secrets Operator uses two CRDs: `AembitEdgeApiClient` defines the connection to Aembit, and `AembitSecretRefreshSchedule` defines which credentials to retrieve and how often to refresh them.

### AembitEdgeApiClient

Defines the connection to the Aembit platform, including authentication and host attestation configuration. You typically create one per cluster. Short name: `aeac`.

```yaml
apiVersion: aembit.io/v1
kind: AembitEdgeApiClient
metadata:
  name: aembit-connection
spec:
  tenantId: "<your-tenant-id>"
  clientId: "<your-edge-sdk-client-id>"
  # stackDomain: "useast2.aembit.io"  # Optional: hostname suffix; URL is https://{tenantId}.ec.{stackDomain}
  # resourceSetId: "<your-resource-set-id>"              # Optional
  attestations:
    oidc:                            # Option 1: OIDC symmetric key (HS256)
      source: generated
      signingSecretRef:
        name: aembit-oidc-signing-key
        secretField: key
      # claimsConfigMapRef:          # Optional: OIDC claims
      #   name: aembit-oidc-claims
    # kubernetesServiceAccount: {}              # Option 2: default SA token path
    # kubernetesServiceAccount:                # Option 2: custom projected-volume path
    #   tokenPath: /var/run/secrets/custom/token
    # host:                          # Optional: host attestation passthrough
    #   passthroughPath: /run/aembit/host_attestations.json
```

> **Authentication options aren’t mutually exclusive**
>
> You can specify both `attestations.oidc` and `attestations.kubernetesServiceAccount` together. When you configure both, Secrets Operator uses the authentication method that matches your Aembit Trust Provider configuration. Use both simultaneously during phased migrations or when supporting multiple Trust Provider configurations.

#### AembitEdgeApiClient spec fields

| Field                                             | Type   | Required | Description                                                                                                                                                                                                                                                                                                                                                                            |
| ------------------------------------------------- | ------ | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `tenantId`                                        | string | Yes      | Your Aembit tenant ID.                                                                                                                                                                                                                                                                                                                                                                 |
| `clientId`                                        | string | Yes      | The Edge SDK Client ID from your Aembit tenant. Find this on the Trust Provider details page in the Aembit UI.                                                                                                                                                                                                                                                                         |
| `stackDomain`                                     | string | No       | Hostname suffix used to construct the Aembit Edge API URL. Defaults to `useast2.aembit.io`. The full URL is `https://{tenantId}.ec.{stackDomain}`.                                                                                                                                                                                                                                     |
| `resourceSetId`                                   | string | No       | Resource Set ID for scoped access. Omit to use the default resource set.                                                                                                                                                                                                                                                                                                               |
| `attestations.oidc.source`                        | string | No       | OIDC token source. Only `generated` is supported.                                                                                                                                                                                                                                                                                                                                      |
| `attestations.oidc.signingSecretRef.name`         | string | No       | Name of the Kubernetes Secret containing the OIDC symmetric signing key.                                                                                                                                                                                                                                                                                                               |
| `attestations.oidc.signingSecretRef.namespace`    | string | No       | Namespace of the signing key Secret. Defaults to the same namespace as this resource. Must be the same namespace—cross-namespace references cause errors.                                                                                                                                                                                                                              |
| `attestations.oidc.signingSecretRef.secretField`  | string | No       | Key within the Secret that holds the signing key value.                                                                                                                                                                                                                                                                                                                                |
| `attestations.oidc.claimsConfigMapRef.name`       | string | No       | Name of a ConfigMap containing OIDC claims as key-value pairs. Can include standard claims like `iss` and `sub` or application-specific claims.                                                                                                                                                                                                                                        |
| `attestations.oidc.claimsConfigMapRef.namespace`  | string | No       | Namespace of the claims ConfigMap. Defaults to the same namespace as this resource. Must be the same namespace—cross-namespace references cause errors.                                                                                                                                                                                                                                |
| `attestations.kubernetesServiceAccount`           | object | No       | Enable Kubernetes Service Account token authentication. Set to `{}` to use the default SA token path, or provide `tokenPath` to use a custom projected-volume path. Use alone or alongside `attestations.oidc`. See [Kubernetes Service Account Trust Provider](../../../access-policies/trust-providers/kubernetes-service-account-trust-provider.md) for tenant-side configuration. |
| `attestations.kubernetesServiceAccount.tokenPath` | string | No       | Path to the projected ServiceAccount token file. Defaults to `/var/run/secrets/kubernetes.io/serviceaccount/token`. Set this when using a custom projected volume with a specific audience.                                                                                                                                                                                            |
| `attestations.host.passthroughPath`               | string | No       | Path within the Secrets Operator pod where Secrets Operator mounts the host attestations file. Required when using host attestation Access Conditions.                                                                                                                                                                                                                                 |

#### AembitEdgeApiClient status

| Field        | Type         | Description                                                                             |
| ------------ | ------------ | --------------------------------------------------------------------------------------- |
| `phase`      | string       | Current state: `Ready` or `Error`.                                                      |
| `conditions` | \[]Condition | Standard Kubernetes conditions. The `Ready` condition indicates the resource is usable. |

### AembitSecretRefreshSchedule

Defines which credential to retrieve, where to store it, and how often to refresh. Create one resource for each credential Secrets Operator should manage. Short name: `asrs`.

```yaml
apiVersion: aembit.io/v1
kind: AembitSecretRefreshSchedule
metadata:
  name: vault-token-schedule
spec:
  aembitEdgeApiClientRef:
    name: aembit-connection
  targetSecretName: vault-token
  # credentialType: OAuthToken  # Optional: OAuthToken (default), ApiKey, UsernamePassword, AwsStsFederation, GoogleWorkloadIdentityFederation, or X509Svid
  server:
    host: vault.example.com
    port: 8200
  # refreshInterval: "4h"
```

#### AembitSecretRefreshSchedule spec fields

| Field                              | Type     | Required | Description                                                                                                                                                                                                                                                                                                                                                          |
| ---------------------------------- | -------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `aembitEdgeApiClientRef.name`      | string   | Yes      | Name of the `AembitEdgeApiClient` resource to use for authentication.                                                                                                                                                                                                                                                                                                |
| `aembitEdgeApiClientRef.namespace` | string   | No       | Namespace of the `AembitEdgeApiClient`. Defaults to the same namespace as this resource. Must be the same namespace—cross-namespace references cause errors.                                                                                                                                                                                                         |
| `targetSecretName`                 | string   | Yes      | Name of the Kubernetes Secret where Secrets Operator stores the credential. Secrets Operator creates this Secret if it doesn’t exist. Must be a valid DNS subdomain name (lowercase alphanumeric, `-` or `.`).                                                                                                                                                       |
| `credentialType`                   | string   | No       | Selects which Credential Provider type Aembit uses for this schedule. Must match the type of the Credential Provider in your Access Policy. One of `OAuthToken` (default), `ApiKey`, `UsernamePassword`, `AwsStsFederation`, `GoogleWorkloadIdentityFederation`, or `X509Svid`. See [Credential types and Secret data keys](#credential-types-and-secret-data-keys). |
| `server.host`                      | string   | Yes      | Hostname or IP address of the target server (for example, your Vault instance).                                                                                                                                                                                                                                                                                      |
| `server.port`                      | integer  | Yes      | Port of the target server (1–65535).                                                                                                                                                                                                                                                                                                                                 |
| `server.transportProtocol`         | string   | No       | Transport protocol. `TCP` (default) or `UDP`.                                                                                                                                                                                                                                                                                                                        |
| `refreshInterval`                  | duration | No       | How often Secrets Operator proactively refreshes the credential. Accepts Go duration format (for example, `4h`, `30m`, `168h` for 7 days). If not set, Secrets Operator refreshes at 80% of the credential’s expiry time, or defaults to `1h` if no expiry appears in the response.                                                                                  |

> **Force sync**
>
> Set or update the `aembit.io/force-sync` annotation on the resource to trigger an immediate credential refresh. Secrets Operator detects annotation value changes and resyncs regardless of the next scheduled refresh time.

#### Credential types and Secret data keys

`spec.credentialType` selects which Credential Provider type Aembit uses for this schedule. The keys Secrets Operator writes into the target Secret mirror the Aembit Edge API credentials response, which each type typically produces as follows:

| `spec.credentialType`              | Secret data keys                                          | Notes                                                                                                                                                                                                                                                                                                                                                                                                                        |
| ---------------------------------- | --------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `OAuthToken`                       | `token`                                                   | Default when `credentialType` is omitted. Covers HashiCorp Vault client tokens and other OAuth-style bearer tokens.                                                                                                                                                                                                                                                                                                          |
| `ApiKey`                           | `apiKey`                                                  |                                                                                                                                                                                                                                                                                                                                                                                                                              |
| `UsernamePassword`                 | `username`, `password`                                    | Ready to consume from a database connection string or a basic-auth client.                                                                                                                                                                                                                                                                                                                                                   |
| `AwsStsFederation`                 | `awsAccessKeyId`, `awsSecretAccessKey`, `awsSessionToken` | Secrets Operator redacts these values from its debug logs.                                                                                                                                                                                                                                                                                                                                                                   |
| `GoogleWorkloadIdentityFederation` | `token`                                                   | A distinct credential type from `OAuthToken`, even though both write a single `token` key.                                                                                                                                                                                                                                                                                                                                   |
| `X509Svid`                         | `tls.crt`, `tls.key`                                      | Writes a `kubernetes.io/tls` Secret instead of a generic Secret. `tls.crt` holds the signed certificate chain (leaf and intermediates, no root CA); `tls.key` holds the private key generated in-cluster. SPIFFE ID, lifetime, and Extended Key Usage come from the Credential Provider, not the schedule. See [Deliver X.509-SVID certificates with Secrets Operator](x509-svid.md). |

Because the keys mirror the Edge API response, they can depend on your Server Workload configuration rather than on `credentialType` alone. For example, a `UsernamePassword` Credential Provider can return a single base64-encoded `token` key—instead of `username` and `password`—when the Server Workload uses an Authorization bearer header.

> **Omitting `credentialType` is backward compatible**
>
> A schedule that doesn’t set `credentialType` defaults to `OAuthToken` and writes a single `token` key—exactly the behavior before this field existed. Existing HashiCorp Vault and cert-manager configurations need no change.

> **`credentialType` must match the Credential Provider**
>
> The value you set must match the type of the Credential Provider in the Access Policy that applies to Secrets Operator. On a mismatch, the Aembit Edge API returns a successful response with no populated fields. Rather than writing a blank Secret, the schedule enters the `Error` phase and reports `Aembit Edge API returned a credentials response with no populated fields` in its [`lastErrorDesc`](#aembitsecretrefreshschedule-status) status field.

> **Switching to or from `X509Svid` recreates the Secret**
>
> The `X509Svid` type writes a `kubernetes.io/tls` Secret; every other type writes a generic Secret. Because a Kubernetes Secret’s `type` is immutable, changing an existing schedule’s `credentialType` to or from `X509Svid` makes Secrets Operator delete and recreate the target Secret. Any workload mounting the Secret sees it disappear and reappear.

### AembitSecretRefreshSchedule status

Secrets Operator reports status on each `AembitSecretRefreshSchedule` resource. Inspect with:

```shell
kubectl describe aembitsecretrefreshschedule <name>
```

| Field                    | Type      | Description                                                                                                                                                           |
| ------------------------ | --------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `phase`                  | string    | Current state: `Ready` or `Error`.                                                                                                                                    |
| `lastSyncTime`           | timestamp | When Secrets Operator last successfully retrieved and wrote credentials to the target Secret.                                                                         |
| `nextRefreshTime`        | timestamp | When Secrets Operator next attempts to refresh the credential.                                                                                                        |
| `credentialExpiresAt`    | timestamp | When the current credential expires.                                                                                                                                  |
| `consecutiveFailures`    | integer   | Number of consecutive failed retrieval attempts. Used for exponential backoff. Resets to 0 on success.                                                                |
| `errorBackoffExpiresAt`  | timestamp | When Secrets Operator next retries after a failure. Set while in the `Error` phase; reconciliation waits until this time before the next attempt. Cleared on success. |
| `lastErrorDesc`          | string    | Human-readable description of the most recent error that caused the schedule to enter the `Error` phase. Inspect this first when troubleshooting a failing schedule.  |
| `syncedSecret.name`      | string    | Name of the Kubernetes Secret containing the credential.                                                                                                              |
| `syncedSecret.namespace` | string    | Namespace of the Kubernetes Secret containing the credential.                                                                                                         |

The `kubectl get` output includes these print columns for quick status checks:

```shell
kubectl get aembitsecretrefreshschedule --namespace aembit-system
```

```plaintext
NAME                   EDGE API CLIENT     TARGET        PHASE   LAST SYNC              NEXT REFRESH           AGE
vault-token-schedule   aembit-connection   vault-token   Ready   2026-03-19T10:00:00Z   2026-03-19T14:00:00Z   2d
```

Secrets Operator also emits Kubernetes events for state changes. View them with:

```shell
kubectl get events --namespace <namespace> --field-selector involvedObject.name=<schedule-name>
```

## Environment variables

The following environment variables override default Secrets Operator configuration when set.

> **Note**
>
> These environment variables correspond to Helm chart values (`logging.level`, `health.port`, `errorHandling.*`). In most deployments, configure these through `values.yaml` rather than setting environment variables directly.

### `AEMBIT_LOG_LEVEL`

Default `info`

Logging verbosity. One of: `off`, `debug`, `info`, `warn`, `error`.

***

### `AEMBIT_HEALTH_PROBE_ADDR`

Default `:8081`

Address and port for health probes.

***

### `AEMBIT_INITIAL_BACKOFF_INTERVAL`

Default `1m`

Wait time after the first reconciliation error.

***

### `AEMBIT_MAX_BACKOFF_INTERVAL`

Default `15m`

Maximum wait time after repeated errors (exponential backoff cap).

***

### `POD_NAMESPACE`

Default - not set

Secrets Operator namespace. The Helm chart sets this automatically via the Downward API. Falls back to reading the service account namespace file.

## Host attestations

When your Access Policy includes Access Conditions that validate device posture, Secrets Operator can pass host-level attributes from the Kubernetes node to Aembit. For example, you can forward CrowdStrike device identity data for verification.

A JSON file on each node collects host attestation data. The file mounts into the Secrets Operator pod via a `hostPath` volume. Secrets Operator reads this file and includes the data in authentication requests.

### Enabling host attestations

1. Prepare the host attestations file on each node. The file must contain a JSON object that satisfies the `host` field of the [Edge API authentication schema](../../../../dev-guide/api/edge/overview.md#visual-tree-diagram). Place the file at the configured path (default: `/run/aembit/host_attestations.json`).

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

2. Enable host attestations in the Helm chart:

   **values.yaml**

   ```yaml
   hostAttestations:
     enabled: true
     hostPath: /run/aembit/host_attestations.json
     mountPath: /run/aembit/host_attestations.json
   ```

3. Reference the mount path in your `AembitEdgeApiClient` resource:

   ```yaml
   spec:
     attestations:
       host:
         passthroughPath: /run/aembit/host_attestations.json
   ```

### Security considerations

* Host attestations use **read-only** `hostPath` mounts. Secrets Operator doesn’t write to the host filesystem.
* Enabling host attestations requires a Pod Security Admission profile of `baseline` or `privileged`. The `restricted` profile doesn’t allow `hostPath` volumes.
* Aembit validates all claimed attributes against the configured backend (for example, CrowdStrike). A compromised node can’t forge another node’s hardware identity.
