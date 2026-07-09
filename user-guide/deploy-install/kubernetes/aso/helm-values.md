---
type: how-to
title: "Secrets Operator Helm chart values"
description: "Key Helm chart values and environment variable reference for Aembit Secrets Operator"
resource: https://docs.aembit.io/user-guide/deploy-install/kubernetes/aso/helm-values/
interface: web-ui
tags: [aso, kubernetes, deploy-install]
timestamp: 2026-06-18T13:15:52-04:00
type_inferred: true
---

# Secrets Operator Helm chart values

This page provides key Helm chart values and environment variable reference for Aembit Secrets Operator**Aembit Secrets Operator**: Aembit Secrets Operator is a Kubernetes operator that authenticates to the Aembit platform and synchronizes credentials into Kubernetes Secrets for applications to consume directly.[Learn more](overview.md) (ASO). The chart name is `aembit-secrets-operator` (version `1.31.314`).

## Image configuration

[Section titled “Image configuration”](#image-configuration)

### `image.repository`

[Section titled “image.repository”](#imagerepository)

Default `aembit/aembit-secrets-operator`

Container image repository.

***

### `image.tag`

[Section titled “image.tag”](#imagetag)

Default Chart `appVersion`

Image tag. Defaults to the chart’s `appVersion` if empty.

***

### `image.pullPolicy`

[Section titled “image.pullPolicy”](#imagepullpolicy)

Default `IfNotPresent`

Image pull policy. Use `IfNotPresent` (default) or `Always` for production. Use `Never` for local development with kind/minikube (requires pre-loaded image).

***

### `image.pullSecrets`

[Section titled “image.pullSecrets”](#imagepullsecrets)

Default `[]`

Image pull secrets for private registries.

## Host attestations

[Section titled “Host attestations”](#host-attestations)

### `hostAttestations.enabled`

[Section titled “hostAttestations.enabled”](#hostattestationsenabled)

Default `false`

Enable host attestation passthrough. Requires `hostPath` volume mounts and a Pod Security Admission profile of `baseline` or `privileged`.

***

### `hostAttestations.hostPath`

[Section titled “hostAttestations.hostPath”](#hostattestationshostpath)

Default `/run/aembit/host_attestations.json`

Path on the host to the attestations file.

***

### `hostAttestations.mountPath`

[Section titled “hostAttestations.mountPath”](#hostattestationsmountpath)

Default `/run/aembit/host_attestations.json`

Mount path within the Secrets Operator pod. Must match the `attestations.host.passthroughPath` in your `AembitEdgeApiClient` resource.

## Error handling

[Section titled “Error handling”](#error-handling)

### `errorHandling.initialBackoffInterval`

[Section titled “errorHandling.initialBackoffInterval”](#errorhandlinginitialbackoffinterval)

Default `1m`

Retry interval after the first reconciliation error.

***

### `errorHandling.maxBackoffInterval`

[Section titled “errorHandling.maxBackoffInterval”](#errorhandlingmaxbackoffinterval)

Default `15m`

Maximum retry interval for exponential backoff.

## Operator configuration

[Section titled “Operator configuration”](#operator-configuration)

### `replicaCount`

[Section titled “replicaCount”](#replicacount)

Default `1`

Number of Secrets Operator replicas.

***

### `resources.limits.cpu`

[Section titled “resources.limits.cpu”](#resourceslimitscpu)

Default `500m`

CPU resource limit.

***

### `resources.limits.memory`

[Section titled “resources.limits.memory”](#resourceslimitsmemory)

Default `128Mi`

Memory resource limit.

***

### `resources.requests.cpu`

[Section titled “resources.requests.cpu”](#resourcesrequestscpu)

Default `10m`

CPU resource request.

***

### `resources.requests.memory`

[Section titled “resources.requests.memory”](#resourcesrequestsmemory)

Default `64Mi`

Memory resource request.

***

### `logging.level`

[Section titled “logging.level”](#logginglevel)

Default `info`

Log level: `debug`, `info`, `warn`, `error`, `off`. Use `debug` only for troubleshooting in non-production environments.

***

### `health.port`

[Section titled “health.port”](#healthport)

Default `8081`

Port for health probes.

## Security context

[Section titled “Security context”](#security-context)

### `securityContext.runAsNonRoot`

[Section titled “securityContext.runAsNonRoot”](#securitycontextrunasnonroot)

Default `true`

Require non-root user.

***

### `securityContext.runAsUser`

[Section titled “securityContext.runAsUser”](#securitycontextrunasuser)

Default `65532`

UID for the Secrets Operator process.

***

### `containerSecurityContext.readOnlyRootFilesystem`

[Section titled “containerSecurityContext.readOnlyRootFilesystem”](#containersecuritycontextreadonlyrootfilesystem)

Default `true`

Read-only root filesystem.

***

### `containerSecurityContext.allowPrivilegeEscalation`

[Section titled “containerSecurityContext.allowPrivilegeEscalation”](#containersecuritycontextallowprivilegeescalation)

Default `false`

Prevent privilege escalation.

## Scheduling

[Section titled “Scheduling”](#scheduling)

### `nodeSelector`

[Section titled “nodeSelector”](#nodeselector)

Default `{}`

Node selector for pod scheduling.

***

### `tolerations`

[Section titled “tolerations”](#tolerations)

Default `[]`

Tolerations for pod scheduling.

***

### `affinity`

[Section titled “affinity”](#affinity)

Default `{}`

Affinity rules for pod scheduling.

***

### `topologySpreadConstraints`

[Section titled “topologySpreadConstraints”](#topologyspreadconstraints)

Default `[]`

Topology spread constraints for pod distribution.

***

### `podDisruptionBudget.enabled`

[Section titled “podDisruptionBudget.enabled”](#poddisruptionbudgetenabled)

Default `true`

Enable PodDisruptionBudget. Only takes effect when `replicaCount` > 1.

***

### `podDisruptionBudget.minAvailable`

[Section titled “podDisruptionBudget.minAvailable”](#poddisruptionbudgetminavailable)

Default `1`

Minimum available pods during voluntary disruptions.

***

### `podDisruptionBudget.maxUnavailable`

[Section titled “podDisruptionBudget.maxUnavailable”](#poddisruptionbudgetmaxunavailable)

Default not set

Maximum number of pods that can be unavailable during voluntary disruptions. Mutually exclusive with `podDisruptionBudget.minAvailable` — set one or the other, not both.

## RBAC

[Section titled “RBAC”](#rbac)

### `rbac.create`

[Section titled “rbac.create”](#rbaccreate)

Default `true`

Create the `Role`, `RoleBinding`, `ClusterRole`, and `ClusterRoleBinding` resources required by Secrets Operator. Set to `false` when you manage RBAC out of band (for example, via GitOps with a separate security team).

## Service account

[Section titled “Service account”](#service-account)

### `serviceAccount.create`

[Section titled “serviceAccount.create”](#serviceaccountcreate)

Default `true`

Create a ServiceAccount for the Secrets Operator pod. Set to `false` to use an existing ServiceAccount you manage out of band.

***

### `serviceAccount.name`

[Section titled “serviceAccount.name”](#serviceaccountname)

Default `""`

Name of the ServiceAccount. Defaults to the chart’s full name when empty. When `serviceAccount.create` is `false`, set this to the name of your existing ServiceAccount.

***

### `serviceAccount.annotations`

[Section titled “serviceAccount.annotations”](#serviceaccountannotations)

Default `{}`

Annotations applied to the ServiceAccount. Common uses include AWS IAM Roles for Service Accounts (IRSA) (`eks.amazonaws.com/role-arn`) and GCP Workload Identity (`iam.gke.io/gcp-service-account`) to federate the in-cluster ServiceAccount to a cloud IAM identity.

## Pod configuration

[Section titled “Pod configuration”](#pod-configuration)

### `podAnnotations`

[Section titled “podAnnotations”](#podannotations)

Default `{}`

Additional annotations applied to the Secrets Operator pod. Common uses include Istio sidecar injection (`sidecar.istio.io/inject`), Prometheus scrape config, and secret-store Container Storage Interface (CSI) driver annotations.

***

### `podLabels`

[Section titled “podLabels”](#podlabels)

Default `{}`

Additional labels applied to the Secrets Operator pod. Useful for selector matching by network policies, service meshes, and observability tooling.

***

### `extraEnv`

[Section titled “extraEnv”](#extraenv)

Default `[]`

Additional environment variables for the Secrets Operator container. Accepts the standard Kubernetes `EnvVar` schema, including `valueFrom.secretKeyRef` and `valueFrom.configMapKeyRef`.

***

### `extraVolumes`

[Section titled “extraVolumes”](#extravolumes)

Default `[]`

Additional volumes attached to the Secrets Operator pod. Pair with `extraVolumeMounts` when using a projected ServiceAccount token at a custom path. See the `attestations.kubernetesServiceAccount.tokenPath` field on `AembitEdgeApiClient`.

***

### `extraVolumeMounts`

[Section titled “extraVolumeMounts”](#extravolumemounts)

Default `[]`

Additional volume mounts for the Secrets Operator container. Pair with `extraVolumes` to mount external content into the pod, such as a projected ServiceAccount token at a custom path.
