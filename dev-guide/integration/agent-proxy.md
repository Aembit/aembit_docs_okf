---
type: how-to
title: "Integrate through Agent Proxy"
description: "The developer-side procedure for running an application behind Agent Proxy, from placeholder credential to verified request"
resource: https://docs.aembit.io/dev-guide/integration/agent-proxy/
interface: web-ui
tags: ["integration"]
timestamp: 2026-08-27T18:01:41-07:00
---

# Integrate through Agent Proxy

Agent Proxy intercepts your application’s outbound requests. Before each request reaches the target Server Workload, Agent Proxy injects the credential the request needs. The integration work therefore lives in your infrastructure and your Aembit configuration, and your application code changes only where a client library demands a credential value. This page walks you through the developer-side work: supply a placeholder credential where a library requires one, then send a request and verify the credential arrived. For the other ways to integrate, see the [Developer Guide overview](../overview.md).

## Prerequisites

Complete both prerequisites before changing application code, because Agent Proxy only injects a credential that an Access Policy authorizes:

* [Deploy Agent Proxy](../../user-guide/deploy-install/overview.md) in the environment where your application runs.
* Create an Access Policy that authorizes your Client Workload to reach the target Server Workload, with a Trust Provider and a Credential Provider attached. You can create the Access Policy in the [Tenant UI](../../user-guide/access-policies/overview.md) of your Aembit Tenant, through the [Cloud API](../api/cloud/overview.md), or with the Terraform provider, and [Manage your Aembit configuration](../overview.md#manage-your-aembit-configuration) compares the three paths.

## Add a placeholder credential

Many client libraries refuse to initialize without a credential value, even though Aembit supplies the real credential at request time. Give such a library a **placeholder credential**, which is any non-empty string that passes the library’s validation, such as `'placeholder-client-secret'`. Agent Proxy replaces the placeholder with the real credential in transit, so the placeholder never reaches the target service.

```python
from some_oauth_library import OAuthClient


# Initialize with a placeholder - Aembit replaces it in transit
client = OAuthClient(
    client_id='your-client-id',
    client_secret='placeholder-client-secret',
    token_url='https://oauth-provider.com/token'
)
```

Some libraries attach the credential per request instead of at initialization, such as an API key in a header. In that case you may have no code change at all, because Agent Proxy adds the header for you. [Client library patterns for Agent Proxy](client-library-patterns.md) shows where the placeholder goes for OAuth SDKs, API key headers, and database drivers, and links the official SDK documentation for common services.

## Send a request and verify

1. Run your application in the environment where you deployed Agent Proxy.

2. Make a request to the target Server Workload.

3. Confirm the request succeeded, then check the authorization event in your Aembit Tenant.

[Test and debug your integration](testing.md) covers the verification end to end, including authorization events, credential delivery checks, and the common failure modes.

## How the credential reaches the service

The Server Workload’s configured authentication method and scheme determine how Agent Proxy attaches the injected credential to your request. The credential can arrive as an `Authorization` header, a named header, a query parameter, a protocol-level password, or a certificate in the TLS handshake. Knowing the configured combination tells you what the target service receives, which is what you compare against when the target rejects a request. For choosing and configuring a combination in your Aembit Tenant, see [Authentication methods and schemes](../../user-guide/access-policies/server-workloads/authentication.md).

Most methods inject credentials into the application protocol. mTLS Authentication presents the credential during the TLS handshake, beneath the application protocol.

The following table lists all supported combinations of authentication methods and schemes, along with their compatible application protocols and credential providers:

| Auth Method              | Auth Scheme      | Application Protocols                   | Credential Provider | Description                                                                                                                                                               | Specification                                                                                                                 |
| ------------------------ | ---------------- | --------------------------------------- | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| HTTP Authentication      | Basic            | HTTP                                    | Username & Password | Encodes `username:password` in Base64 and sends it in the HTTP `Authorization` header.                                                                                    | [The ‘Basic’ HTTP Authentication Scheme](https://datatracker.ietf.org/doc/html/rfc7617)                                       |
| HTTP Authentication      | Bearer           | HTTP                                    | Any single-value    | Sends a `Bearer` token in the HTTP `Authorization` header.                                                                                                                | [Bearer Token Usage](https://datatracker.ietf.org/doc/html/rfc9700)                                                           |
| HTTP Authentication      | Header           | HTTP                                    | Any single-value    | Injects credentials into a user-defined HTTP header as part of HTTP authentication flow.                                                                                  | n/a                                                                                                                           |
| HTTP Authentication      | AWS Signature v4 | HTTP                                    | AWS STS Federation  | Signs the HTTP request using AWS Signature v4.                                                                                                                            | [Create a signed AWS API request](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_sigv-create-signed-request.html) |
| API Key                  | Header           | HTTP                                    | Any single-value    | Injects API key credentials into a user-defined HTTP header for API-based authentication.                                                                                 | n/a                                                                                                                           |
| API Key                  | Query parameter  | HTTP                                    | Any single-value    | Injects credentials into a user-defined HTTP query parameter.                                                                                                             | n/a                                                                                                                           |
| Password Authentication  | Password         | MySQL, Postgres, Amazon Redshift, Redis | Username & Password | Injects credentials according to protocol-specific requirements. Applies only to protocols with a single auth method.                                                     | n/a                                                                                                                           |
| JWT Token Authentication | Snowflake JWT    | Snowflake                               | JWT                 | Modifies the body of an HTTP request to `/session/v1/login-request`, injecting `USERNAME` and `TOKEN`.                                                                    | n/a                                                                                                                           |
| mTLS Authentication      | x509 Certificate | HTTP                                    | X.509-SVID          | Authenticates an Access Policy’s Client Workload and Server Workload using a SPIFFE-compliant X.509-SVID certificate that Agent Proxy presents during the mTLS handshake. | [SPIFFE X.509-SVID Standard](https://github.com/spiffe/spiffe/blob/main/standards/X509-SVID.md)                               |

> **Agent Proxy can also add static HTTP headers**
>
> A Server Workload can define static HTTP headers, which are name and value pairs that Agent Proxy adds to every HTTP request it forwards to that workload. If your request already carries a header with the same name, the static value replaces it, so check the Server Workload definition before debugging an unexpected header value. See [About static HTTP headers](../../user-guide/access-policies/server-workloads/static-http-headers.md).

## Next steps

* **[Client library patterns for Agent Proxy](client-library-patterns.md)** - Placeholder placement for each library type, plus service-specific SDK resources
* **[Test and debug your integration](testing.md)** - Verify interception, credential delivery, and access end to end
* **[Local development](local-development.md)** - Get credentials while developing on your own machine
* **[Server Workload guides](../../user-guide/access-policies/server-workloads/guides/overview.md)** - Service-specific configuration
