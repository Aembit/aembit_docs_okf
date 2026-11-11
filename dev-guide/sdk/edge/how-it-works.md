---
type: explanation
title: "How the Edge SDK works"
description: "How the Edge SDK authenticates a workload, keeps its access token current, and retrieves credentials, and which Aembit components decide each step."
resource: https://docs.aembit.io/dev-guide/sdk/edge/how-it-works/
interface: sdk
tags: ["edge", "sdk"]
timestamp: 2026-11-10T10:42:48-08:00
---

# How the Edge SDK works

The Edge SDK gets a credential for your application in two requests to the [Aembit Edge API](../../api/edge/overview.md). The authentication request proves the workload’s identity and returns an access token. The credential request presents that token and returns the credential an Access Policy grants. The TypeScript and Python SDKs behave the same way except where this page says otherwise. The page names each method in both languages, and the developer reference for [TypeScript](https://github.com/Aembit/edge-sdks/tree/main/ts#readme) and [Python](https://github.com/Aembit/edge-sdks/tree/main/py#readme) covers installation, Trust Provider setup, and examples.

## The request flow

Your application calls one method, `getCredential()` in TypeScript or `get_credential()` in Python. The SDK authenticates first only when its cached access token cannot serve the request, as [When the SDK authenticates again](#when-the-sdk-authenticates-again) describes:

![Sequence showing the Edge SDK collecting identity evidence, authenticating to Aembit Cloud when its cached access token cannot serve the request, then requesting the credential that Aembit Cloud returns after Access Policy evaluation](https://docs.aembit.io/d2/docs/dev-guide/sdk/edge/how-it-works-0.svg)

### Identity evidence

The SDK collects identity evidence at the start of every credential request, even when it holds a valid access token. The Trust Provider object you pass to the client decides where the evidence comes from. A metadata service Trust Provider, such as AWS Metadata Service or Azure Instance Metadata Service, reads a signed identity document from the platform. The AWS Role Trust Provider signs a request with the AWS credentials available to the workload. A token Trust Provider, such as OIDC ID Token or GitHub, uses the identity token your application passes. You pass it as a fixed string or as a function that the SDK calls each time. The Python Kubernetes Service Account Trust Provider reads the service account token file when you pass no token.

### Authentication

The authentication request carries two things: the client ID and the identity evidence. Aembit reads the Trust Provider from the client ID and confirms it is active. It then verifies the evidence against that Trust Provider. When the evidence passes, Aembit returns an access token, and the SDK caches it.

You do not need to call `authenticate()` yourself, because the credential method authenticates when it needs to. Call it when you want an authentication failure to surface early, such as at application startup. It returns the token’s expiry time and the ID of the SDK’s Trust Provider object, and never the access token itself. Each call to `authenticate()` sends a new authentication request, even when the cached token is still valid. The exception is a matching authentication request already in flight, which the call waits for and shares.

### Credential retrieval

The credential request carries the access token and the identity evidence. It also names the host and port of the service your application needs to reach, and the credential type you ask for. Aembit uses the evidence to identify the Client Workload, and the host and port to identify the Server Workload. It then finds the Access Policy that connects the two, checks the Access Policy’s Trust Providers and Access Conditions, and asks its Credential Provider for the credential.

The SDK does not cache credentials. Every call to the credential method sends a credential request, even when the access token comes from the cache. When the credential carries an expiry time, your application can reuse it until that time if the service it is for allows that.

## How the SDK manages the access token

The SDK holds the access token in memory, inside the client object. Two client objects never share a token, and nothing survives a process restart, so a new process authenticates again. Create one client and reuse it for the life of your application.

### When the SDK authenticates again

The SDK checks the cached token each time your application requests a credential. It sends a new authentication request when any of these holds:

* The client holds no access token yet.
* The cached token expires within the expiry window, which is 60 seconds by default.
* The request names a different Resource Set than the one Aembit issued the cached token for.
* In TypeScript, a token Trust Provider returns a different identity token than the one the cached token came from.

The SDK refreshes only at that check. Nothing renews the token in the background, so a client that sits idle past the token’s expiry authenticates on its next credential request.

The expiry window makes the SDK treat a token as expired before Aembit does, so a request does not carry a token that is about to expire. Set it in milliseconds with `authExpirySkewMs` in TypeScript or `auth_expiry_skew_ms` in Python.

A client caches one token at a time. An application that alternates between two Resource Sets on one client authenticates on every switch, so use one client for each Resource Set.

In Python, a token Trust Provider whose function returns a new identity token keeps using the cached access token until the expiry window. Aembit still reads the new identity token from each credential request.

### Concurrent requests

When two or more credential requests need a new token at the same moment, the SDK sends one authentication request and gives its result to each of them. TypeScript does this for concurrent promises, and Python does it for concurrent threads on one client. Requests share an authentication request only when they name the same Resource Set and use the same retry settings. In TypeScript, they must also carry the same identity token from a token Trust Provider. A call to `authenticate()` also joins a matching authentication request already in flight.

The SDK also collects identity evidence once and shares it among concurrent requests, for Trust Providers that allow it. Requests that do not overlap still collect fresh evidence each time. That sharing covers the metadata service and AWS Role Trust Providers, and token Trust Providers given a fixed string. A token Trust Provider given a function, or the Python Kubernetes Service Account Trust Provider reading its token file, collects evidence for each request.

### When authentication fails

When an authentication request fails, the SDK raises the error to your application. It also discards the cached token when that token is within its expiry window or past it, and Aembit issued it for the failed request’s Resource Set. A cached token outside its expiry window stays in place. The SDK does not authenticate again when a credential request fails, so a `401` from the credential request reaches your application as an error.

## How the SDK maps to Aembit components

Each value you give the SDK lines up with a component you configure in your Aembit Tenant:

| What you give the SDK                            | Aembit component    | What Aembit does with it                                              |
| ------------------------------------------------ | ------------------- | --------------------------------------------------------------------- |
| The client ID                                    | Trust Provider      | Picks the Trust Provider that verifies the authentication request     |
| The Trust Provider object                        | Trust Provider      | Supplies evidence, which must suit the configured Trust Provider type |
| The identity evidence in each credential request | Client Workload     | Matches the Client Workload’s **Client Identification** values        |
| The host and port of the target service          | Server Workload     | Matches the **Host** and **Port** under **Service Endpoint**          |
| Nothing directly                                 | Access Policy       | Connects the Client Workload to the Server Workload                   |
| Connection metadata, when you set it             | Credential Provider | Picks one of the Access Policy’s Credential Providers                 |
| The credential type                              | Credential Provider | Picks which fields of the credential Aembit fills                     |
| The Resource Set ID, when you set one            | Resource Set        | Scopes the Trust Provider lookup and the Access Policy search         |

When the credential comes back empty or the request fails, the [Edge SDK quickstart troubleshooting](quickstart.md#troubleshooting) section works through each of these components in turn.

## The client ID is not a Client Workload identifier

The `clientId` setting, `client_id` in Python, takes the Edge SDK Client ID from a Trust Provider’s page in your Aembit Tenant. It identifies that Trust Provider, not your workload. It looks like `aembit:useast2:a12bc3:identity:github_idtoken:63ab7be6-9785-4a14-be1c-2acf0253070b`, and it ends with the Trust Provider’s ID.

None of the values under a Client Workload’s **Client Identification** belong in `clientId`. That includes the Aembit Client ID, a UUID that Aembit generates for one Client Workload. Aembit matches the Client Workload from the identity evidence in the credential request, and never from the client ID. Aembit rejects a `clientId` that is not in the Edge SDK Client ID format, such as an Aembit Client ID UUID, with a `400` status. It does so before it looks up a Trust Provider or checks any identity evidence. An Edge SDK Client ID from another Aembit Tenant gets the same `400`.

Because the client ID names a Trust Provider, every workload that attests through that Trust Provider uses the same client ID. When those workloads need different credentials, tell them apart with **Client Identification** values that their evidence matches. The [identifier reference](../../../reference/identifiers.md) compares the Edge SDK Client ID, the Aembit Client ID, and the Client Workload resource ID.

## Related resources

* [Get started with the Aembit Edge SDK](quickstart.md) configures each component and retrieves a first credential.
* [Edge SDK logging and network activity](logging.md) lists the log events that trace token caching and authentication.
* [Aembit Edge API](../../api/edge/overview.md) documents the two requests the SDK sends.
