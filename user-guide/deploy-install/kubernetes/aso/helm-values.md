---
type: reference
title: "Secrets Operator Helm chart values"
description: "Key Helm chart values and environment variable reference for Aembit Secrets Operator"
resource: https://docs.aembit.io/user-guide/deploy-install/kubernetes/aso/helm-values/
interface: web-ui
tags: ["aso", "kubernetes", "deploy-install"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Secrets Operator Helm chart values

This page provides key Helm chart values and environment variable reference for

Aembit Secrets Operator (ASO). The chart name is `aembit-secrets-operator` (version `1.31.314`).

## Image configuration

### `image.repository`

Default `aembit/aembit-secrets-operator`

Container image repository.

***

### `image.tag`

Default Chart `appVersion`

Image tag. Defaults to the chart’s `appVersion` if empty.

***

### `image.pullPolicy`

Default `IfNotPresent`

Image pull policy. Use `IfNotPresent` (default) or `Always` for production. Use `Never` for local development with kind/minikube (requires pre-loaded image).

***

### `image.pullSecrets`

Default `[]`

Image pull secrets for private registries.

## Host attestations

### `hostAttestations.enabled`

Default `false`

Enable host attestation passthrough. Requires `hostPath` volume mounts and a Pod Security Admission profile of `baseline` or `privileged`.

***

### `hostAttestations.hostPath`

Default `/run/aembit/host_attestations.json`

Path on the host to the attestations file.

***

### `hostAttestations.mountPath`

Default `/run/aembit/host_attestations.json`

Mount path within the Secrets Operator pod. Must match the `attestations.host.passthroughPath` in your `AembitEdgeApiClient` resource.

## Error handling

### `errorHandling.initialBackoffInterval`

Default `1m`

Retry interval after the first reconciliation error.

***

### `errorHandling.maxBackoffInterval`

Default `15m`

Maximum retry interval for exponential backoff.

## Operator configuration

### `replicaCount`

Default `1`

Number of Secrets Operator replicas.

***

### `resources.limits.cpu`

Default `500m`

CPU resource limit.

***

### `resources.limits.memory`

Default `128Mi`

Memory resource limit.

***

### `resources.requests.cpu`

Default `10m`

CPU resource request.

***

### `resources.requests.memory`

Default `64Mi`

Memory resource request.

***

### `logging.level`

Default `info`

Log level: `debug`, `info`, `warn`, `error`, `off`. Use `debug` only for troubleshooting in non-production environments.

***

### `health.port`

Default `8081`

Port for health probes.

## Security context

### `securityContext.runAsNonRoot`

Default `true`

Require non-root user.

***

### `securityContext.runAsUser`

Default `65532`

UID for the Secrets Operator process.

***

### `containerSecurityContext.readOnlyRootFilesystem`

Default `true`

Read-only root filesystem.

***

### `containerSecurityContext.allowPrivilegeEscalation`

Default `false`

Prevent privilege escalation.

## Scheduling

### `nodeSelector`

Default `{}`

Node selector for pod scheduling.

***

### `tolerations`

Default `[]`

Tolerations for pod scheduling.

***

### `affinity`

Default `{}`

Affinity rules for pod scheduling.

***

### `topologySpreadConstraints`

Default `[]`

Topology spread constraints for pod distribution.

***

### `podDisruptionBudget.enabled`

Default `true`

Enable PodDisruptionBudget. Only takes effect when `replicaCount` > 1.

***

### `podDisruptionBudget.minAvailable`

Default `1`

Minimum available pods during voluntary disruptions.

***

### `podDisruptionBudget.maxUnavailable`

Default not set

Maximum number of pods that can be unavailable during voluntary disruptions. Mutually exclusive with `podDisruptionBudget.minAvailable` — set one or the other, not both.

## RBAC

### `rbac.create`

Default `true`

Create the `Role`, `RoleBinding`, `ClusterRole`, and `ClusterRoleBinding` resources required by Secrets Operator. Set to `false` when you manage RBAC out of band (for example, via GitOps with a separate security team).

## Service account

### `serviceAccount.create`

Default `true`

Create a ServiceAccount for the Secrets Operator pod. Set to `false` to use an existing ServiceAccount you manage out of band.

***

### `serviceAccount.name`

Default `""`

Name of the ServiceAccount. Defaults to the chart’s full name when empty. When `serviceAccount.create` is `false`, set this to the name of your existing ServiceAccount.

***

### `serviceAccount.annotations`

Default `{}`

Annotations applied to the ServiceAccount. Common uses include AWS IAM Roles for Service Accounts (IRSA) (`eks.amazonaws.com/role-arn`) and GCP Workload Identity (`iam.gke.io/gcp-service-account`) to federate the in-cluster ServiceAccount to a cloud IAM identity.

## Pod configuration

### `podAnnotations`

Default `{}`

Additional annotations applied to the Secrets Operator pod. Common uses include Istio sidecar injection (`sidecar.istio.io/inject`), Prometheus scrape config, and secret-store Container Storage Interface (CSI) driver annotations.

***

### `podLabels`

Default `{}`

Additional labels applied to the Secrets Operator pod. Useful for selector matching by network policies, service meshes, and observability tooling.

***

### `extraEnv`

Default `[]`

Additional environment variables for the Secrets Operator container. Accepts the standard Kubernetes `EnvVar` schema, including `valueFrom.secretKeyRef` and `valueFrom.configMapKeyRef`.

***

### `extraVolumes`

Default `[]`

Additional volumes attached to the Secrets Operator pod. Pair with `extraVolumeMounts` when using a projected ServiceAccount token at a custom path. See the `attestations.kubernetesServiceAccount.tokenPath` field on `AembitEdgeApiClient`.

***

### `extraVolumeMounts`

Default `[]`

Additional volume mounts for the Secrets Operator container. Pair with `extraVolumes` to mount external content into the pod, such as a projected ServiceAccount token at a custom path.
