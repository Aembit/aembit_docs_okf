---
type: how-to
title: "Vault Dynamic Claims"
description: "Configure dynamic claims for Vault Client Token Credential Providers"
resource: https://docs.aembit.io/user-guide/access-policies/credential-providers/advanced-options/dynamic-claims-vault/
interface: web-ui
tags: ["advanced-option", "credential-provider", "access-policy"]
timestamp: 2026-09-22T11:51:31-07:00
---

# Vault Dynamic Claims

> **Note**
>
> This page describes dynamic claims for **Vault Client Token Credential Providers** only. For OIDC ID Token, JWT-SVID, and SAML dynamic claims, see [Dynamic Claims](dynamic-claims.md).

Dynamic claims allow you to make Vault credential configuration dynamic in nature, enabling workloads to specify workload-specific claim values outside of the Aembit Tenant UI.

When working with Vault Client Token Credential Providers for your Aembit Tenant, you have the option to enable the dynamic claims feature. With this feature, you can set either a subject claim, or a custom claim, with either literal strings or dynamic values.

## Minimum versions

To use the dynamic claims feature, you must also update Agent Injector to the new minimum version/image so the `aembit.io/agent-configmap` annotation works as expected.

## Literal strings

You can place literal strings verbatim into the target claim with no modification or adjustment necessary.

## Dynamic values

Aembit Cloud communicates dynamic claim requests to Agent Proxy following these steps:

1. Aembit Cloud sends the template to Agent Proxy.

2. Agent Proxy collects all necessary information and then sends this information to Aembit Cloud.

3. Aembit Cloud replaces template variables with the values provided by Agent Proxy.

The following sections describe how you can support Vault with Aembit dynamic claims.

## Configuring HashiCorp Vault Cloud

To enable dynamic claims, you must first configure your HashiCorp Vault instance, since dynamic claims are only applicable to Vault Client Token Credential Providers. Aembit supports dynamic claims for the Vault Client Token Credential Provider, you must also configure Vault to support a matching set of values.

Vault OIDC roles, which Aembit uses to log into Vault as part of the Vault client token retrieval, support one or more of the following three bound types:

* `bound_subject`
* `bound_audiences`
* generically bound claims

For more detailed information on configuring Vault Cloud, see [Use JWT/OIDC authentication](https://developer.hashicorp.com/vault/docs/auth/jwt#configuration) HashiCorp Vault docs.

## Client Workload configuration

If you need to use values from ConfigMap as dynamic claims, you need to configure the `aembit.io/agent-configmap` annotation for the Client Workload.

For the latest release, you can add this new annotation to a deployment similar to the following code snippet:

```yaml
rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      annotations:
        aembit.io/agent-configmap: '["agent-controller-config:device_code"]'
        aembit.io/agent-inject: enabled
      creationTimestamp: null
      labels:
        name: globex-portal
    spec:
      containers:
      - env:
        - name: AEMBIT_API_BASE_ADDRESS
          value: 'https://12ab3c.aembit.io/api/v1/'
        - name: AEMBIT_ACCESS_TOKEN
```

The Agent Proxy supports Kubernetes ConfigMaps and specific environment variables in dynamic claims.

Aembit supports the following templates:

* `k8s.configmap.*.*".`\
  Make sure to specify the `CONFIGMAP` and `VALUE` (represented by ”*.*”).

* `os.environment.*.*.`\
  Make sure to specify `"K8S_POD_NAME"` (represented by *.*).

* `os.environment.*.*`\
  Make sure to specify `CLIENT_WORKLOAD_ID` (represented by ”*.*”).

## Client Workload Kubernetes annotations

For the Client Workload to retrieve and configure ConfigMap, you must correctly annotate the Client Workload. For the latest release, you can add this new annotation to a deployment similar to the following code snippet:

```yaml
rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      annotations:
        aembit.io/agent-configmap: '["agent-controller-config:device_code"]'
        aembit.io/agent-inject: enabled
      creationTimestamp: null
      labels:
        name: globex-portal
    spec:
      containers:
      - env:
        - name: AEMBIT_API_BASE_ADDRESS
          value: 'https://12ab3c.aembit.io/api/v1/'
        - name: AEMBIT_ACCESS_TOKEN
```

## Confirm Aembit authentication to Vault

If the Client Workload is able to successfully connect to Vault, this confirms that Aembit authenticated to Vault with the configured and correctly injected dynamic claims.
