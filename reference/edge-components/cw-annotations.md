---
type: reference
title: "Client Workload annotation reference"
description: "Reference for Kubernetes annotations you can apply to Client Workload pod specs to configure Agent Proxy behavior"
resource: https://docs.aembit.io/reference/edge-components/cw-annotations/
tags: [edge-component]
timestamp: 2026-06-18T13:15:52-04:00
type_inferred: true
---

# Client Workload annotation reference

When you deploy Client Workloads to a Kubernetes cluster with Aembit Edge, you use pod annotations to control Agent Proxy behavior. These annotations go in the `metadata.annotations` section of your pod template spec.

For deployment steps, see [Deploy Aembit to Kubernetes](../../user-guide/deploy-install/kubernetes/kubernetes.md).

| Annotation                                                                                                                   | Description                                                                                                                                     |
| ---------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| [`aembit.io/agent-inject`](cw-annotations.md#aembitioagent-inject)                                   | Required Enables Agent Proxy injection for this deployment. *Example*: `"enabled"`                                                              |
| [`aembit.io/resource-set-id`](cw-annotations.md#aembitioresource-set-id)                             | Sets the Resource Set ID for Agent Proxy associated with pods in this deployment. *Example*: `"f251f0c5-5681-42f0-a374-fef98d9a5005"`           |
| [`aembit.io/client-id`](cw-annotations.md#aembitioclient-id)                                         | Sets the Aembit Client ID for Agent Proxy associated with pods in this deployment. *Example*: `"7e75e718-7634-480b-9f7b-a07bb5a4f11d"`          |
| [`aembit.io/agent-proxy-env-<ENV_VAR_NAME>`](cw-annotations.md#aembitioagent-proxy-env-env_var_name) | Sets a specific environment variable on Agent Proxy container for pods in this deployment. *Example*: `"false"`                                 |
| [`aembit.io/agent-configmap`](cw-annotations.md#aembitioagent-configmap)                             | Mounts Kubernetes ConfigMap data into Agent Proxy container for pods in this deployment. *Example*: `'["agent-controller-config:device_code"]'` |
| [`aembit.io/steering-mode`](cw-annotations.md#aembitiosteering-mode)                                 | Sets the steering mode for Agent Proxy. Required on OpenShift. *Example*: `"explicit"`                                                          |

## `aembit.io/agent-inject` Required

[Section titled “aembit.io/agent-inject ”](#aembitioagent-inject)

Value - `"enabled"`

Enables Agent Proxy injection for the deployment. Agent Injector watches for this annotation and injects an Agent Proxy sidecar container into matching pods.

This annotation is mandatory. Without it, no other Aembit Client Workload annotations have any effect.

*Example*:

```yaml
template:
  metadata:
    annotations:
      aembit.io/agent-inject: "enabled"
```

For detailed deployment steps, see [Annotate Client Workloads](../../user-guide/deploy-install/kubernetes/kubernetes.md#step-3---annotate-client-workloads).

***

## `aembit.io/resource-set-id`

[Section titled “aembit.io/resource-set-id”](#aembitioresource-set-id)

Value - Resource Set UUID

Sets the Resource Set ID for Agent Proxy associated with pods in this deployment. Once you add this annotation, Aembit Edge injects the Resource Set ID into Agent Proxy, enabling it to support Client Workloads in the specified Resource Set.

You can find the Resource Set ID in your Aembit Tenant under **Administration > Resource Sets**.

*Example*:

```yaml
template:
  metadata:
    annotations:
      aembit.io/agent-inject: "enabled"
      aembit.io/resource-set-id: "f251f0c5-5681-42f0-a374-fef98d9a5005"
```

For more information, see [Resource Sets overview](../../user-guide/administration/resource-sets/overview.md) and [Deploy a Resource Set](../../user-guide/administration/resource-sets/deploy-resource-set.md).

***

## `aembit.io/client-id`

[Section titled “aembit.io/client-id”](#aembitioclient-id)

Value - Client Workload UUID

Sets the Aembit Client ID for Agent Proxy associated with pods in this deployment. The Aembit Client ID is a fallback identification method for Client Workloads when other identification methods (such as hostname or source IP) are unavailable.

*Example*:

```yaml
template:
  metadata:
    annotations:
      aembit.io/agent-inject: "enabled"
      aembit.io/client-id: "7e75e718-7634-480b-9f7b-a07bb5a4f11d"
```

For more information, see [Aembit Client ID](../../user-guide/access-policies/client-workloads/identification/aembit-client-id.md).

***

## `aembit.io/agent-proxy-env-<ENV_VAR_NAME>`

[Section titled “aembit.io/agent-proxy-env-\<ENV\_VAR\_NAME>”](#aembitioagent-proxy-env-env_var_name)

Value - Environment variable value

Sets a specific environment variable on Agent Proxy container for pods in this deployment. Replace `<ENV_VAR_NAME>` with the name of the environment variable you want to set.

This is useful when you need to configure Agent Proxy behavior on a per-deployment basis rather than cluster-wide through Helm values.

*Example*:

```yaml
template:
  metadata:
    annotations:
      aembit.io/agent-inject: "enabled"
      aembit.io/agent-proxy-env-AEMBIT_PASS_THROUGH_TRAFFIC_BEFORE_REGISTRATION: "false"
```

For available environment variables, see [Edge Component environment variables](edge-component-env-vars.md).

***

## `aembit.io/agent-configmap`

[Section titled “aembit.io/agent-configmap”](#aembitioagent-configmap)

Value - JSON array of `"ConfigMapName:KeyName"` pairs

Mounts Kubernetes ConfigMap data into Agent Proxy container for pods in this deployment. Agent Injector reads this annotation and adds volume mounts so Agent Proxy can access the specified ConfigMap keys at runtime.

The Edge Component mounts ConfigMap data to `/mnt/agent-proxy/<ConfigMapName>/<KeyName>` inside Agent Proxy container. This is primarily used with the [Vault dynamic claims](../../user-guide/access-policies/credential-providers/advanced-options/dynamic-claims-vault.md) feature, where Agent Proxy reads ConfigMap values and includes them in workload assessments.

ConfigMaps must be in the same namespace as the Client Workload pod. If a referenced ConfigMap doesn’t exist, Agent Proxy still starts, but the dynamic claim value resolves to an empty string.

*Example*:

```yaml
template:
  metadata:
    annotations:
      aembit.io/agent-inject: "enabled"
      aembit.io/agent-configmap: '["my-configmap:my-key"]'
```

For more information, see [Vault Dynamic Claims](../../user-guide/access-policies/credential-providers/advanced-options/dynamic-claims-vault.md).

***

## `aembit.io/steering-mode`

[Section titled “aembit.io/steering-mode”](#aembitiosteering-mode)

Value - `"explicit"` | `"transparent"`

Sets the steering mode for Agent Proxy. The default behavior when this annotation is omitted is transparent steering.

* **`"explicit"`** — Client Workloads must configure themselves to use Agent Proxy as an HTTP or HTTPS proxy. No automatic traffic interception occurs.
* **`"transparent"`** — Agent Proxy transparently intercepts pod traffic using iptables rules injected by Agent Injector. Client Workloads require no additional configuration or code changes. This is the default behavior when the annotation is omitted.

Not all platforms support both steering modes. For a full comparison of steering mode support across deployment models (including Kubernetes, OpenShift, and Fargate), see the [method comparison table](../../user-guide/deploy-install/advanced-options/agent-proxy/steering.md#method-comparison-and-protocol-support).

*Example*:

```yaml
template:
  metadata:
    annotations:
      aembit.io/agent-inject: "enabled"
      aembit.io/steering-mode: "explicit"
```

For more information, see [Steering](../../user-guide/deploy-install/advanced-options/agent-proxy/steering.md) and [Explicit steering](../../user-guide/deploy-install/advanced-options/agent-proxy/explicit-steering.md).
