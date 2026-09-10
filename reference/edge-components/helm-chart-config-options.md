---
type: reference
title: "Edge Component Helm chart configuration options reference"
description: "Reference for Helm chart configuration options when deploying Aembit to Kubernetes"
resource: https://docs.aembit.io/reference/edge-components/helm-chart-config-options/
tags: ["edge-component"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Edge Component Helm chart configuration options reference

The Aembit Helm Chart includes configuration options that control the behavior of Aembit Edge Components (Agent Controller, Agent Proxy, and Agent Injector). To deploy those components, the chart deploys additional Kubernetes resources, such as a Service Account and a webhook. The chart also allows you to specify ad-hoc annotations to each of these resources.

* [Behavior configuration](#edge-component-behavior-configuration)

* [Resource annotations](#edge-component-resource-annotations)

## Edge component behavior configuration

### `tenant` Required

Default - not set

The Aembit Tenant ID that Edge Components use.

*Example*:\
`123abc`

***

### `agentController.deviceCode` Required

Default - not set

Required if not using `agentController.id`.

Aembit uses device codes for code-based registration of Agent Controllers, which you can generate in your tenant’s Aembit administrator console. You must provide either this or the `agentController.id` value.

*Example*:\
`123456`

***

### `agentController.id` Required

Default - not set

Required if not using `agentController.deviceCode`.

Aembit uses this unique ID for attestation-based registration of Agent Controllers, which you can find in the Aembit administrator console. You must provide either this or the `agentController.deviceCode` value.

*Example*:\
`01234567-89ab-cdef-0123-456789abcdef`

***

### `agentController.tls.secretName`

Default - not set

The name of a [Kubernetes TLS secret](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_create/kubectl_create_secret_tls/) containing a private key and certificate used for Agent Controller TLS.

*Example*:\
`aembit_ac_tls`

***

### `agentInjector.filters.namespaces`

Default - not set

This configuration specifies the Kubernetes namespaces where Aembit injects the Agent Project as a sidecar into Client Workloads.

*Example*:\
`{namespace1, namespace2}`

***

### `agentInjector.env`

Default - not set

This allows you to specify a list of environment variables for the Agent Injector. You can pass it to Helm using the `-f <filename>` option (to pass a values file) or directly via `--set "agentInjector.env.AEMBIT_SOME_ENV=some_value"`.

*Example*:\
`AEMBIT_SOME_ENV=some_value`

***

### `agentProxy.trustedCertificates`

Default - not set

A base64 encoded list of PEM-encoded certificates that the Agent Proxy trusts. For more information, please refer to [Trusting Private CA](../../user-guide/deploy-install/advanced-options/trusting-private-cas.md).

If you set the `agentProxy.trustedCertificatesVolumeName` parameter, it overrides this option.

*Example*:\
`L1S2L3S4L5C6R7U8D9F0I1C2A3T4E5`

***

### `agentProxy.trustedCertificatesVolumeName`

Default - not set

Replaces the trusted CA certificates in the Agent Proxy container with the certificates from a volume. This is useful for deployments that don’t permit privilege escalation or that have a read-only filesystem. Since this replaces all existing trusted CA certificates in the container you must provide all certificates necessary to connect to your Server Workloads.

When defining a ConfigMap with your certificate bundle, your key name must be `ca-certificates.crt`.

**Example ConfigMap**

```yaml
  ca-certificates.crt: |
    -----BEGIN CERTIFICATE-----
    MIIFmzCCBSGgAwIBAgIQCtiTuvposLf7ekBPBuyvmjAKBggqhkjOPQQDAzBZMQsw
    ...
```

This option overrides `agentProxy.trustedCertificates`.

*Example*:\
`my-volume`

***

### `agentProxy.env`

Default - not set

This allows you to specify a list of environment variables for the Agent Proxy. You can pass it to Helm using the `-f <filename>` option (to pass a values file) or directly via `--set "agentProxy.env.AEMBIT_SOME_ENV=some_value"`.

*Example*:\
`AEMBIT_SOME_ENV=some_value`

## Edge component resource annotations

The following options accept any annotation names and values that Kubernetes accepts. The values specified with `--set` use the period (`.`) character to separate nested names. Most [Kubernetes annotations](https://kubernetes.io/docs/concepts/overview/working-with-objects/annotations/) use DNS namespace prefixes and thus also include period characters. Be sure to escape the periods in your annotation names using a backslash (`\`) character. Alternatively, specify these in a YAML file with the `-f <filename>` option. No escaping is necessary in this file.

***

### `agentController.deploymentAnnotations`

Default - not set

This affects the annotations applied to the `Deployment` resource for the Agent Controller.

*Example*:\
`--set "agentController.deploymentAnnotations.example\.com/custom-name=custom-value"`

***

### `agentController.podAnnotations`

Default - not set

This affects the annotations applied to the `Pod` resource for the Agent Controller.

*Example*:\
`--set "agentController.podAnnotations.example\.com/custom-name=custom-value"`

***

### `agentController.serviceAnnotations`

Default - not set

This affects the annotations applied to the `Service` resource for the Agent Controller.

*Example*:\
`--set "agentController.serviceAnnotations.example\.com/custom-name=custom-value"`

***

### `agentInjector.deploymentAnnotations`

Default - not set

This affects the annotations applied to the `Deployment` resource for the Agent Injector.

*Example*:\
`--set "agentInjector.deploymentAnnotations.example\.com/custom-name=custom-value"`

***

### `agentInjector.podAnnotations`

Default - not set

This affects the annotations applied to the `Pod` resource for the Agent Injector.

*Example*:\
`--set "agentInjector.podAnnotations.example\.com/custom-name=custom-value"`

***

### `agentInjector.serviceAnnotations`

Default - not set

This affects the annotations applied to the `Service` resource for the Agent Injector.

*Example*:\
`--set "agentInjector.serviceAnnotations.example\.com/custom-name=custom-value"`

***

### `agentInjector.tlsSecretAnnotations`

Default - not set

This affects the annotations applied to the `Secret` resource that stores the generated TLS certificate. The Agent Injector uses this certificate to secure communication with the admission control webhook.

*Example*:\
`--set "agentInjector.tlsSecretAnnotations.example\.com/custom-name=custom-value"`

***

### `agentInjector.webhookAnnotations`

Default - not set

This affects the annotations applied to the `MutatingWebhookConfiguration` resource for the Agent Injector. A common use is to set the [`cert-manager.io/inject-ca-from` annotation](https://cert-manager.io/docs/concepts/ca-injector/) to have cert-manager configure the `caBundle` property of this admission control webhook.

*Example*:\
`--set "agentInjector.webhookAnnotations.example\.com/custom-name=custom-value"`

***

### `agentProxy.runAsRestricted`

Default - not set

Set this to `true` to make the Agent Proxy container definition drop all its privileges, making it compatible with the OpenShift `restricted-v2` [`SecurityContextConstraint`](https://www.redhat.com/en/blog/managing-sccs-in-openshift) or the standard `restricted` [security standard](https://kubernetes.io/docs/concepts/security/pod-security-standards/).

***

### `serviceAccount.openshift.scc`

Default - not set

The Helm chart deploys a `ServiceAccount`. The `Deployment` resources for both the Agent Controller and Agent Injector rely on this service account. Set this to the name of the `SecurityContextConstraint` (SCC) that you want this service account to use.
