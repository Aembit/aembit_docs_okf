---
type: how-to
title: "Aembit Client ID"
description: "This document outlines the Aembit Client ID method for identifying Client Workloads."
resource: https://docs.aembit.io/user-guide/access-policies/client-workloads/identification/aembit-client-id/
interface: web-ui
tags: ["generic", "identification", "client-workload", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Aembit Client ID


The Aembit Client ID method serves as a fallback for Client Workload identification when other suitable methods are unavailable. This method entails generating a unique ID by the Aembit Cloud, which is then provisioned to the Client Workload.

> **The Aembit Client ID isn’t the Client Workload’s resource ID**
>
> Every Client Workload also has its own resource ID, which Aembit assigns automatically and exposes as `externalId` in the API. That resource ID isn’t the Aembit Client ID, and no flag, variable, or annotation accepts it.
>
> Whenever you provision this identifier—through the [`--client-workload-id`](../../../../dev-guide/cli/reference/credentials-get.md#--client-workload-id) flag, the [`CLIENT_WORKLOAD_ID`](../../../../reference/edge-components/edge-component-env-vars.md#client_workload_id) environment variable, or the [`aembit.io/client-id`](../../../../reference/edge-components/cw-annotations.md#aembitioclient-id) annotation—use the value Aembit generates in the following step. Both values use the same UUID format, so confirm you copied the generated one.
>
> For a side-by-side comparison of all three identifiers, see the [Aembit identifier reference](../../../../reference/identifiers.md).

## Applicable deployment type

This method is suitable for Aembit Edge-based deployments.

## Configuration

### Aembit Cloud

1. Create a new Client Workload.
2. Choose “Aembit Client ID” for client identification.
3. Complete the remaining fields.
4. Copy the newly generated ID that Aembit displays for the **Aembit Client ID** identifier.
5. Save the Client Workload.

![Aembit Client ID](https://docs.aembit.io/_astro/client_identification_aembit_client_id.CiS18YKw_Zwx03R.webp)

### Client Workload

#### Virtual machine deployment

During Agent Proxy installation, specify the `CLIENT_WORKLOAD_ID` environment variable.

```shell
CLIENT_WORKLOAD_ID=<ClientWorkloadID> AEMBIT_TENANT_ID=<TenantID> AEMBIT_AGENT_CONTROLLER_ID=<AgentControllerId> ./install
```

#### Kubernetes

Add the `aembit.io/agent-inject` annotation to your Client Workload.

See the following example:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: example-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: example-app
  template:
    metadata:
      labels:
        app: example-app
      annotations:
        aembit.io/agent-inject: "enabled"
        aembit.io/client-id: "7e75e718-7634-480b-9f7b-a07bb5a4f11d"
```

## Related

**Compatible trust providers**

* [AWS Metadata Service](../../trust-providers/aws-metadata-service-trust-provider.md)
* [AWS Role](../../trust-providers/aws-role-trust-provider.md)
* [Azure Instance Metadata Service](../../trust-providers/azure-metadata-service-trust-provider.md)
* [Certificate Signed Attestation](../../trust-providers/certificate-signed-attestation-trust-provider.md)
* [GCP Identity Token](../../trust-providers/gcp-identity-token-trust-provider.md)
* [Kerberos](../../trust-providers/kerberos-trust-provider.md)
* [Kubernetes Service Account](../../trust-providers/kubernetes-service-account-trust-provider.md)
* [OIDC ID Token](../../trust-providers/oidc-id-token-trust-provider.md)
