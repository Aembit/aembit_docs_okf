---
type: explanation
title: "About static HTTP headers"
description: "What static HTTP headers do, when to use them, and why credentials belong in a Credential Provider"
resource: https://docs.aembit.io/user-guide/access-policies/server-workloads/static-http-headers/
tags: ["server-workload", "access-policy"]
timestamp: 2026-08-17T16:37:59-04:00
---

# About static HTTP headers

A Server Workload can define static HTTP headers: fixed header name and value pairs that Agent Proxy adds to every HTTP request it forwards to that workload.

The **Static HTTP Headers** section appears when the Server Workload’s Service Endpoint has Application Protocol set to **HTTP**, which covers most REST and web API targets.

Static HTTP headers belong to the Server Workload, so they apply to every Access Policy that reaches it. Agent Proxy sends them whether or not the policy uses a Credential Provider to inject a credential.

> **Static values override matching headers**
>
> If a request already carries a header with the same name, the static value replaces it.

## When to use static HTTP headers

Use static HTTP headers when a target service requires a header on every request and your application doesn’t send it. For example, Claude’s API requires an `anthropic-version` header, such as `anthropic-version: 2023-06-01`.

Common cases:

* Pin requests to a specific API version
* Identify your account or tenant on the target service, not your Aembit Tenant
* Direct requests through custom routing

> **Static HTTP headers aren’t credentials**
>
> Don’t store secrets, tokens, or API keys in static HTTP headers. Aembit stores them with the Server Workload definition and returns them to anyone with read access to that Server Workload, through both the Aembit Tenant and the Aembit Cloud API.
>
> Version identifiers and routing hints belong in static HTTP headers. Anything that grants access belongs in a Credential Provider.
>
> To send a credential in a header you name, use a Credential Provider with the **Header** authentication scheme instead. See [Authentication methods and schemes](authentication.md).

## Related pages

* [Authentication methods and schemes](authentication.md)
* [Credential lifecycle management](credential-lifecycle.md)
* [About Server Workloads](../../../get-started/concepts/server-workloads.md)
