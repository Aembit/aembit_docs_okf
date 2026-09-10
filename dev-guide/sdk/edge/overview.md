---
type: reference
title: "Aembit Edge SDKs"
description: "Language libraries that let your application authenticate workloads and retrieve credentials through Aembit."
resource: https://docs.aembit.io/dev-guide/sdk/edge/
interface: sdk
tags: ["edge", "sdk"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Aembit Edge SDKs

The Aembit Edge SDKs are language libraries that wrap the [Aembit Edge API](../../api/edge/overview.md). Your application authenticates a workload and retrieves credentials through a few method calls, without hand-rolling HTTP requests or managing token lifecycles. The SDK runs inside your application process, so there’s nothing extra to deploy or operate alongside it.

The SDKs are available for TypeScript and Python.

## When to use an SDK

The [Developer Guide overview](../../overview.md#get-credentials-into-your-workload-at-runtime) compares all four ways to get a credential into a workload: Agent Proxy, the Edge SDK, the Aembit CLI, and the Edge API.

Choose an SDK when you can change the application’s source and you want credential retrieval to be explicit in your code. It suits environments where running a proxy next to the workload isn’t practical, such as serverless functions, ephemeral CI containers, and platforms where you don’t control the runtime.

If changing application code isn’t an option, Agent Proxy reaches the same outcome by intercepting traffic instead. That path trades code changes for components you deploy and operate.

## What the SDKs handle for you

An SDK covers the same three steps you’d otherwise implement yourself against the Edge API:

* **Workload attestation.** The SDK collects identity evidence from the environment it runs in, such as a cloud instance identity document or an OIDC token, and presents that evidence to Aembit. Aembit verifies it against the Trust Provider you configured.
* **Token lifecycle.** The SDK caches the resulting access token in memory and refreshes it before it expires, so repeated calls don’t re-authenticate.
* **Credential retrieval.** The SDK requests a credential for the service your application needs to reach. Aembit returns one only if an Access Policy permits that access.

The SDKs use the same Aembit primitives as every other access path, so the console-side configuration is familiar. You configure a Trust Provider to verify the workload’s identity and a Credential Provider to supply the credential. An Access Policy then authorizes a specific Client Workload to reach a specific Server Workload, using those providers.

## Supported Trust Providers

You can use the Edge SDK with the following Trust Providers:

* [AWS Metadata Service Trust Provider](../../../user-guide/access-policies/trust-providers/aws-metadata-service-trust-provider.md)
* [AWS Role Trust Provider](../../../user-guide/access-policies/trust-providers/aws-role-trust-provider.md)
* [Azure Instance Metadata Service Trust Provider](../../../user-guide/access-policies/trust-providers/azure-metadata-service-trust-provider.md)
* [GCP Identity Token Trust Provider](../../../user-guide/access-policies/trust-providers/gcp-identity-token-trust-provider.md)
* [GitHub Trust Provider](../../../user-guide/access-policies/trust-providers/github-trust-provider.md)
* [GitLab Trust Provider](../../../user-guide/access-policies/trust-providers/gitlab-trust-provider.md)
* [OIDC ID Token Trust Provider](../../../user-guide/access-policies/trust-providers/oidc-id-token-trust-provider.md)

Not every provider is available in both languages. For per-language coverage, see [Edge SDK Trust Providers](../../../reference/support-matrix.md#edge-sdk-trust-providers).

## Where to go next

![Code Icon](https://docs.aembit.io/aembit-icons/code-solid.svg)

[SDK repository](https://github.com/Aembit/edge-sdks)Installation, the developer reference, and runnable examples for each supported environment.

→

![Cloud Icon](https://docs.aembit.io/aembit-icons/cloud-solid.svg)

[Edge API](../../api/edge/overview.md)The REST interface the SDKs wrap, including the authentication and credential endpoints.

→

![Shield Icon](https://docs.aembit.io/aembit-icons/shield-check-solid.svg)

[Trust Providers](../../../user-guide/access-policies/trust-providers/overview.md)Configure how Aembit verifies the identity your workload presents.

→
