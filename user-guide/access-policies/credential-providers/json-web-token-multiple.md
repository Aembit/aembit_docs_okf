---
type: explanation
title: "Using Multiple JWT Credential Providers in a Single Access Policy"
description: "How Aembit routes requests to multiple JWT Credential Providers based on username or HTTP values"
resource: https://docs.aembit.io/user-guide/access-policies/credential-providers/json-web-token-multiple/
tags: [credential-provider, access-policy]
timestamp: 2025-12-04T22:08:22-08:00
---

# Using Multiple JWT Credential Providers in a Single Access Policy

This page explains how Aembit enables the use of multiple JSON Web Token (JWT) Credential Providers**Credential Provider**: Credential Providers obtain the specific access credentials—such as API keys, OAuth tokens, or temporary cloud credentials—that Client Workloads need to authenticate to Server Workloads.[Learn more](../../../get-started/concepts/credential-providers.md) within a single Access Policy**Access Policy**: Access Policies define, enforce, and audit access between Client and Server Workloads by cryptographically verifying workload identity and contextual factors rather than relying on static secrets.[Learn more](../../../get-started/concepts/access-policies.md), allowing flexible credential management for Snowflake and HTTP Server Workloads**Server Workload**: Server Workloads represent target services, APIs, databases, or applications that receive and respond to access requests from Client Workloads.[Learn more](../../../get-started/concepts/server-workloads.md).

Supported Server Workload types

Aembit supports multiple JWT Credential Providers only for Server Workloads with **Snowflake** or **HTTP** Application Protocols.

In environments where multiple users or services need different credentials to access the same Server Workload, configuring separate Access Policies for each credential creates unnecessary complexity. Aembit supports configuring multiple JWT Credential Providers within a single Access Policy, with each Credential Provider mapped to specific selector values.

## Benefits

[Section titled “Benefits”](#benefits)

* **Simplified policy management** - Manage multiple JWT credentials within a single Access Policy instead of creating separate policies for each user or service.
* **Flexible mapping** - Map Credential Providers by Snowflake username or HTTP header/body values to match your application’s request patterns.
* **Seamless application experience** - Applications can access resources with different credentials without code changes or multiple Client Workload**Client Workload**: Client Workloads represent software applications, scripts, or automated processes that initiate access requests to Server Workloads, operating autonomously without direct user interaction.[Learn more](../../../get-started/concepts/client-workloads.md) identities.

## How it works

[Section titled “How it works”](#how-it-works)

After you [configure multiple JWT Credential Providers](json-web-token.md#configure-multiple-jwt-credential-providers) in an Access Policy (each with a unique mapping value), Aembit handles requests as follows:

1. **Request interception** - When an application makes a request, Agent Proxy intercepts it and extracts the mapping value (username for Snowflake, or HTTP header/body value for HTTP workloads).

2. **Credential Provider matching** - Aembit matches the extracted value to the corresponding Credential Provider configured in the Access Policy.

3. **Credential injection** - Aembit retrieves the JWT from the matched Credential Provider and injects it into the request.

## Mapping mechanisms

[Section titled “Mapping mechanisms”](#mapping-mechanisms)

JWT Credential Providers use different mapping mechanisms depending on the Server Workload type.

### Snowflake username mapping

[Section titled “Snowflake username mapping”](#snowflake-username-mapping)

For Snowflake Server Workloads, Aembit maps Credential Providers based on the **username** in the connection request.

**Example scenario:**

* User `analyst_a` needs credentials from `JWT-Provider-A`
* User `analyst_b` needs credentials from `JWT-Provider-B`

Configure each Credential Provider with its corresponding username mapping. When a connection request arrives with a specific username, Aembit automatically selects the matching Credential Provider.

### HTTP header or body mapping

[Section titled “HTTP header or body mapping”](#http-header-or-body-mapping)

For HTTP Server Workloads, Aembit maps Credential Providers based on values in **HTTP headers** or the **HTTP body**.

**Example scenario:**

* Requests with header `X-Service-ID: service-a` use `JWT-Provider-A`
* Requests with header `X-Service-ID: service-b` use `JWT-Provider-B`

Configure each Credential Provider with its corresponding header or body value mapping. When a request arrives with the specified value, Aembit automatically selects the matching Credential Provider.

## Error handling

[Section titled “Error handling”](#error-handling)

The following rules apply when handling requests with multiple JWT Credential Providers:

* If the mapping value in a request doesn’t match any configured Credential Provider, Aembit denies the request.
* If Aembit can’t extract the mapping value (for example, missing header), credentials aren’t injected and the request fails.
* Each mapping value must be unique across all Credential Providers in the Access Policy.

## Related topics

[Section titled “Related topics”](#related-topics)

* [Configure a JWT Credential Provider](json-web-token.md) - Set up JWT Credential Providers and configure multiple Credential Providers in an Access Policy
* [Configure multiple Credential Providers](multiple-credential-providers.md) - Overview of multiple Credential Provider support
* [Credential Providers overview](overview.md) - Overview of all available Credential Provider types
* [Snowflake Server Workload](../server-workloads/guides/snowflake.md) - Configure Aembit to work with Snowflake
* [Access Policies](../overview.md) - Learn about Aembit Access Policies and how they work
* [Access Authorization Events](../../audit-report/access-authorization-events.md) - Review access authorization event information in the Reporting Dashboard
