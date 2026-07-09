---
type: how-to
title: "Authentication methods and schemes"
description: "This document describes the configuration of Authentication Methods and Schemes for Server workloads."
resource: https://docs.aembit.io/user-guide/access-policies/server-workloads/authentication/
interface: web-ui
tags: [server-workload, access-policy]
timestamp: 2026-05-21T15:38:50-07:00
type_inferred: true
---

# Authentication methods and schemes

Aembit offers a variety of authentication methods and schemes to secure access to Server Workloads. These configurations define how Credential Providers inject or present credentials. This page details the supported authentication methods and helps you choose the right one for your needs.

## Authentication methods and schemes

[Section titled “Authentication methods and schemes”](#authentication-methods-and-schemes)

When you configure access between Client Workloads and Server Workloads, two key elements dictate how Aembit injects credentials into a request:

* **Authentication Method** - Specifies the general type of authentication in use—for example, HTTP authentication or a database-specific protocol.
* **Authentication Scheme** - Defines the specific implementation of the method. For example, the `Bearer` scheme for HTTP authentication specifies how the credential appears in the HTTP headers.

These elements work together to determine how the Client Workload authenticates to the Server Workload. Additionally, some combinations of authentication methods and schemes may require extra configuration, such as specifying the name of the HTTP header that carries the credential.

Aembit supports combinations of methods and schemes to meet diverse protocol and workload requirements.

## Credential requirements

[Section titled “Credential requirements”](#credential-requirements)

Most authentication methods rely on a single credential that a Credential Provider generates, ensuring broad compatibility. However, some methods use two-part credentials (for example: a username and password), which restricts them to Credential Providers that supply such data.

Additionally, some authentication schemes depend on specific Credential Providers. While you may use them with others, they typically target a particular provider.

## Choosing the right method and scheme

[Section titled “Choosing the right method and scheme”](#choosing-the-right-method-and-scheme)

Selecting the appropriate method and scheme is essential to ensure the Client Workload can successfully authenticate to the Server Workload. Consider the following:

* **Server Workload Requirements** - What methods does the Server Workload support?
* **Security Considerations** - What level of security do you need?
* **Credential Provider Capabilities** - Which providers can generate the required credentials?

Aembit includes method/scheme recommendations for common Server Workloads in Server Workload guides. If your Server Workload doesn’t appear in those guides, use the following guidance to choose and configure an appropriate method and scheme.

## Supported authentication methods and schemes

[Section titled “Supported authentication methods and schemes”](#supported-authentication-methods-and-schemes)

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
