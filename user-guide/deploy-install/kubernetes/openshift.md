---
type: how-to
title: "OpenShift"
description: "Aembit Edge Component deployment considerations in an OpenShift cluster"
resource: https://docs.aembit.io/user-guide/deploy-install/kubernetes/openshift/
interface: web-ui
tags: [kubernetes, deploy-install]
timestamp: 2025-08-22T14:59:25-07:00
type_inferred: true
---

# OpenShift

The Aembit Helm chart supports deploying to OpenShift, including Red Hat OpenShift Service on AWS (ROSA). This page explains the unique considerations when deploying to OpenShift.

Caution

You must use v1.25 of the Aembit Helm chart and all Aembit Edge Components when deploying on OpenShift. If possible, start fresh with the latest Edge Components and the latest Helm chart. Avoid attempting to upgrade from a previously failed installation on OpenShift.

## ServiceAccount and SecurityContextConstraint resources

[Section titled “ServiceAccount and SecurityContextConstraint resources”](#serviceaccount-and-securitycontextconstraint-resources)

OpenShift provides an additional layer of security policy in terms of `SecurityContextConstraint` (SCC). Each SCC limits the options available to `Pod` resources, including those embedded in `Deployment` resources. Your cluster rejects any pod that uses [a disallowed option](https://docs.redhat.com/en/documentation/openshift_container_platform/4.19/html/authentication_and_authorization/managing-pod-security-policies#security-context-constraints-about_configuring-internal-oauth) within its `securityContext` field.

### SCC for Agent Controller and Agent Injector

[Section titled “SCC for Agent Controller and Agent Injector”](#scc-for-agent-controller-and-agent-injector)

When you deploy the Aembit Helm chart, specify an SCC that you expect the Agent Controller and Agent Injector to run under by setting:

```plaintext
--set serviceAccount.openshift.scc=anyuid
```

Choose `anyuid` unless you need to use a custom SCC. If you use a custom SCC, grant it these permissions:

```yaml
allowPrivilegeEscalation: false
allowedCapabilities:
    - NET_BIND_SERVICE
runAsUser:
    type: RunAsAny
```

The Aembit Helm chart uses the permissions of the user or the service account deploying the chart to create a new `ServiceAccount` resource named `aembit`. It then gives the `aembit` service account permission to `use` the named SCC. Each service account is only able to grant permissions it has for itself. To avoid permission errors at this step, [ensure the helm chart deployer has permission](https://www.redhat.com/en/blog/managing-sccs-in-openshift) to `use` the named SCC or else this fails. For a test deployment, deploy as a user with the `cluster-admin` role. When deploying the helm chart using a service account, run the following command to see if the service account appears in the list:

```shell
oc adm policy who-can use SecurityContextConstraints <anyuid or custom SCC name>
```

Deploying the Aembit Helm chart from ArgoCD presents an additional challenge by concealing the service account that ArgoCD uses to configure your cluster. Contact your ArgoCD administrator to ask them to confirm that ArgoCD’s service account is in the output of the `who-can` command.

### SCC for Client Workloads and Agent Proxy

[Section titled “SCC for Client Workloads and Agent Proxy”](#scc-for-client-workloads-and-agent-proxy)

OpenShift admits your pod under the most restrictive SCC that’s both compatible with the `securityContext` field specified in your pod and compatible with the container image specified in your pod. OpenShift [considers each of the SCCs available to the deployer](https://docs.redhat.com/en/documentation/openshift_container_platform/4.19/html/authentication_and_authorization/managing-pod-security-policies#admission_configuring-internal-oauth) of the pod.

The Agent Proxy is capable of running under the `restricted-v2`. To accomplish this, first set:

```plaintext
--set agentProxy.runAsRestricted=true
```

With this set, the Agent Proxy container definition uses a `securityContext` that is compatible with the `restricted-v2` SCC. However, the container image declares an empty `User` value. This retains compatibility with the many supported deployment options for the container image. OpenShift considers this an intention to run as the `root` user, triggering OpenShift to admit the Client Workload pod under any of the deployer’s SCCs that allows running as the `root` user. To ensure your Client Workload pod runs under the `restricted-v2` SCC whenever possible, deploy it using a service account that only has permission to use the `restricted-v2` SCC.

## Explicit versus transparent steering

[Section titled “Explicit versus transparent steering”](#explicit-versus-transparent-steering)

When you deploy Pods to your cluster, annotate them to opt into [explicit steering](../advanced-options/agent-proxy/explicit-steering.md). Configure your Client Workloads to use the Agent Proxy as an HTTP or HTTPS proxy.

```yaml
metadata:
  annotations:
    aembit.io/agent-inject: "enabled"
    aembit.io/steering-mode: "explicit"
```

To provide transparent steering, the Agent Init container needs to install `iptables` rules. OpenShift clusters don’t expose these features to pods. When you configure your Client Workload pod for explicit steering, the Agent Injector omits the Agent Init container. If you configure your Client Workload pod for transparent steering, expect to see these errors in the `aembit-init-iptable` container logs:

```plaintext
owner: Could not determine whether revision 1 is supported, assuming it is.
CT: Could not determine whether revision 2 is supported, assuming it is.
iptables v1.8.9 (legacy): can't initialize iptables table `raw': Permission denied (you must be root)
Perhaps iptables or your kernel needs to be upgraded.
```
