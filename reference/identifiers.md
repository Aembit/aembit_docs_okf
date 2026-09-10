---
type: reference
title: "Aembit identifier reference"
description: "Compare the identifiers you supply to Aembit CLI and Edge Components, including the three similarly named Client Workload identifiers"
resource: https://docs.aembit.io/reference/identifiers/
timestamp: 2026-09-08T23:32:41-07:00
---

# Aembit identifier reference

Aembit generates identifiers for tenants, Trust Providers, Client Workloads, and Resource Sets, and you supply some of them to Aembit CLI or an Edge Component at runtime. Three of them refer to Client Workloads and have similar names, so this page states which value each flag, environment variable, and annotation accepts.

## Which identifier to supply

| Your task                                                          | Identifier                     | How you supply it                                                                                                              |
| ------------------------------------------------------------------ | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------ |
| Request credentials with Aembit CLI                                | Edge SDK Client ID             | `--client-id` or `AEMBIT_CLIENT_ID` (Aembit CLI)                                                                               |
| Select one Client Workload when two or more share a Trust Provider | Aembit Client ID               | `--client-workload-id` (Aembit CLI), `CLIENT_WORKLOAD_ID` (Aembit CLI, Agent Proxy), `aembit.io/client-id` (Kubernetes)        |
| Register an Agent Controller with your tenant                      | Tenant ID, Agent Controller ID | `AEMBIT_TENANT_ID`, `AEMBIT_AGENT_CONTROLLER_ID` (Agent Controller)                                                            |
| Scope a request to a Resource Set                                  | Resource Set ID                | `--resource-set-id` (Aembit CLI), `AEMBIT_RESOURCE_SET_ID` (Aembit CLI, Agent Proxy), `aembit.io/resource-set-id` (Kubernetes) |
| Look up a Client Workload in your tenant or through the API        | Client Workload resource ID    | No flag, variable, or annotation accepts this value                                                                            |

## Client Workload identifiers

These three identifiers all relate to Client Workloads, and two of them share the same UUID format. Supplying the wrong one causes Access Policy matching to fail.

| Identifier                  | What it identifies                | Where to find it                                                        | How you supply it                                                                                                       |
| --------------------------- | --------------------------------- | ----------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| Edge SDK Client ID          | Your tenant and a Trust Provider  | The Trust Provider’s page in your Aembit Tenant                         | `--client-id`, `AEMBIT_CLIENT_ID` (Aembit CLI)                                                                          |
| Aembit Client ID            | One specific Client Workload      | The Client Workload’s **Aembit Client ID** Client Identification method | `--client-workload-id` (Aembit CLI), `CLIENT_WORKLOAD_ID` (Aembit CLI, Agent Proxy), `aembit.io/client-id` (Kubernetes) |
| Client Workload resource ID | The Client Workload record itself | The Aembit Tenant UI, and `externalId` in the REST API                  | No flag, variable, or annotation accepts this value                                                                     |

### Edge SDK Client ID

The Edge SDK Client ID is a structured identifier that encodes your region, tenant, and Trust Provider. Aembit generates it when you configure a Trust Provider.

Because it identifies a Trust Provider rather than a workload, every Client Workload that attests through the same Trust Provider uses the same Edge SDK Client ID.

*Example*:\
`aembit:useast2:a12bc3:identity:github_idtoken:63ab7be6-9785-4a14-be1c-2acf0253070b`

To retrieve it, see [Find your Edge SDK Client ID](../user-guide/access-policies/trust-providers/get-edge-sdk-client-id.md).

### Aembit Client ID

The Aembit Client ID is a UUID that Aembit generates when you choose **Aembit Client ID** as a Client Workload’s Client Identification method. It identifies a single Client Workload. Supply it when two or more workloads attest through the same Trust Provider and would otherwise match the same Access Policy.

A Client Workload has an Aembit Client ID only if you selected that identification method for it. Aembit matches the value only against that identifier type, so a workload identified solely by another method, such as hostname or source IP, never matches.

*Example*:\
`7e75e718-7634-480b-9f7b-a07bb5a4f11d`

To generate one, see [Aembit Client ID](../user-guide/access-policies/client-workloads/identification/aembit-client-id.md).

### Client Workload resource ID

The Client Workload resource ID is the UUID Aembit assigns to every Client Workload when you create it. It appears in the Aembit Tenant UI and as `externalId` in the REST API, and you use it to reference that workload through the API.

No flag, environment variable, or annotation accepts this value. It uses the same UUID format as the Aembit Client ID.

*Example*:\
`f251f0c5-5681-42f0-a374-fef98d9a5005`

## Why a Client Workload has two UUID values

The two values exist for different reasons, and they behave differently over time.

Aembit creates the resource ID with the workload itself. Every Client Workload has one, whatever identification method you configure, and it stays the same until you delete the workload. That stability is what automation depends on. The REST API addresses a workload by this value, and the Terraform provider uses it as the resource’s identity, so `terraform import` takes it.

The Aembit Client ID exists only when you add that Client Identification method, and Aembit generates a new UUID each time you add one. Removing the identifier and adding it back produces a different value, as does switching the type of an existing identifier to **Aembit Client ID**.

> **A stored Aembit Client ID can stop matching**
>
> Aembit generates a new value each time, so an Aembit Client ID you saved elsewhere can go stale. A value stored in a pipeline definition or a script stops matching after someone removes and re-adds that identifier. Access Policy matching then fails without reporting the stale value as the cause. Copy the current value from the Client Workload again if matching stops working.

## Why error messages report a different ID

Aembit CLI reports the Client Workload **resource ID** in Access Policy matching errors, not the Aembit Client ID:

```text
Error matching access policy. No Access Policy found. Matched client workload ID: [client-workload-id]. Matched server workload ID: [server-workload-id].
```

The phrase `client workload ID` in that message refers to the resource ID. Use it to look the workload up in your tenant or through the API.

Passing that value to `--client-workload-id` fails to match, because the flag accepts the Aembit Client ID instead. For the full message format and the other reasons Aembit reports, see [Access Policy matching errors](../dev-guide/cli/troubleshooting.md#access-policy-matching-errors).

## Other identifiers you supply to Aembit

| Identifier          | What it identifies               | Where to find it                                       | How you supply it                                                          | Format                         |
| ------------------- | -------------------------------- | ------------------------------------------------------ | -------------------------------------------------------------------------- | ------------------------------ |
| Tenant ID           | Your Aembit Tenant               | Your tenant’s URL                                      | `AEMBIT_TENANT_ID`                                                         | Short string, such as `123abc` |
| Agent Controller ID | One Agent Controller             | **Edge Components > Agent Controllers** in your tenant | `AEMBIT_AGENT_CONTROLLER_ID`                                               | UUID                           |
| Device code         | An Agent Controller registration | Generated in your tenant for code-based registration   | `AEMBIT_DEVICE_CODE`                                                       | Six digits, such as `123456`   |
| Resource Set ID     | One Resource Set                 | **Administration > Resource Sets** in your tenant      | `--resource-set-id`, `AEMBIT_RESOURCE_SET_ID`, `aembit.io/resource-set-id` | UUID                           |

Agent Controller registration accepts either `AEMBIT_AGENT_CONTROLLER_ID` or `AEMBIT_DEVICE_CODE`, so you supply one or the other.

For the full list of variables each Edge Component accepts, see [Edge Component environment variables](edge-components/edge-component-env-vars.md) and [Client Workload annotations](edge-components/cw-annotations.md).

## Examples

### Aembit CLI

This request supplies the Edge SDK Client ID as `--client-id` and the target workload’s Aembit Client ID as `--client-workload-id`:

```shell
aembit credentials get \
  --client-id "aembit:useast2:a12bc3:identity:github_idtoken:63ab7be6-9785-4a14-be1c-2acf0253070b" \
  --client-workload-id "7e75e718-7634-480b-9f7b-a07bb5a4f11d" \
  --server-workload-host "api.example.com" \
  --server-workload-port 443
```

See [`aembit credentials get`](../dev-guide/cli/reference/credentials-get.md) for every option.

### Agent Proxy on a virtual machine

Set the workload’s Aembit Client ID in the Agent Proxy service environment:

```shell
CLIENT_WORKLOAD_ID=7e75e718-7634-480b-9f7b-a07bb5a4f11d
```

### Client Workload pod on Kubernetes

Annotate the pod template with the workload’s Aembit Client ID:

```yaml
template:
  metadata:
    annotations:
      aembit.io/agent-inject: "enabled"
      aembit.io/client-id: "7e75e718-7634-480b-9f7b-a07bb5a4f11d"
```
