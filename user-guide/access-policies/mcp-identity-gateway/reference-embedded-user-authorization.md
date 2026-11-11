---
type: reference
title: "Embedded user authorization reference"
description: "Request parameters, lifetimes, errors, and events for embedded user authorization."
resource: https://docs.aembit.io/user-guide/access-policies/mcp-identity-gateway/reference-embedded-user-authorization/
interface: mcp
tags: ["mcp-identity-gateway", "access-policy"]
timestamp: 2026-11-10T23:08:08-08:00
---

# Embedded user authorization reference

Reference for the OAuth requests your application sends in embedded user authorization, and for what Aembit returns. For how the flow works, see [Embedded user authorization](embedded-user-authorization.md). To configure your Aembit Tenant, see [Set up embedded user authorization](setup-embedded-user-authorization.md).

## Client type

Aembit treats your application as a public OAuth client, so your application holds no client secret and uses PKCE for each code exchange. Configure these client settings in your application’s OAuth library:

| Property       | Value                                                                                      |
| -------------- | ------------------------------------------------------------------------------------------ |
| Client ID      | The **Aembit Client ID** that the Aembit Tenant UI shows for an OIDC 1.0 Identity Provider |
| Client secret  | None                                                                                       |
| Grant types    | `authorization_code` and `refresh_token`                                                   |
| PKCE           | Required, with the `S256` method                                                           |
| Scope          | `openid` only                                                                              |
| Aembit consent | None; Aembit shows no consent page                                                         |

## Endpoints

Your application uses two Aembit endpoints. It sends the person’s browser to the authorize endpoint to start the flow, and calls the token endpoint directly to exchange the code and refresh tokens. In each URL, replace `<tenant-id>` with your Aembit Tenant ID and `<stack>` with your Aembit Tenant’s stack:

| Endpoint  | URL                                                          |
| --------- | ------------------------------------------------------------ |
| Authorize | `https://<tenant-id>.id.<stack>.aembit.io/connect/authorize` |
| Token     | `https://<tenant-id>.id.<stack>.aembit.io/connect/token`     |

Your OAuth library can also read both endpoints from the discovery document at `https://<tenant-id>.id.<stack>.aembit.io/.well-known/openid-configuration`.

## Authorize request

The authorize request starts the flow. Your application must send every parameter except `state`. All are standard OAuth parameters except `mcp_server_url`, which is specific to Aembit. Your application sends the person’s browser to the authorize endpoint with a URL that carries these query parameters:

| Parameter               | Required         | Value                                                                                    |
| ----------------------- | ---------------- | ---------------------------------------------------------------------------------------- |
| `client_id`             | Yes              | The **Aembit Client ID** from the Identity Provider’s page in the Aembit Tenant UI       |
| `redirect_uri`          | Yes              | Your application’s callback URL, matching a **Redirect URI** identifier                  |
| `response_type`         | Yes              | `code`                                                                                   |
| `scope`                 | Yes              | `openid`                                                                                 |
| `state`                 | No (recommended) | A value your application generates and checks on return. Aembit returns it unchanged     |
| `code_challenge`        | Yes              | The PKCE challenge                                                                       |
| `code_challenge_method` | Yes              | `S256`                                                                                   |
| `mcp_server_url`        | Yes              | The URL of the MCP server the person is authorizing                                      |
| `resource`              | Yes              | The MCP Identity Gateway URL that the Client-to-Gateway Policy’s Server Workload matches |

`mcp_server_url` is an Aembit parameter. Neither OAuth nor the MCP authorization specification defines it, so your OAuth client must be able to add it to the authorize request.

### `mcp_server_url` rules

`mcp_server_url` must meet every one of these rules, or Aembit rejects the request with `invalid_request`:

* Present in the request
* An absolute URL with a host
* HTTPS
* At most 2048 characters long
* No user information and no fragment
* A host that is not internal

Aembit then matches `mcp_server_url` to the Server Workload of one Gateway-to-Server Policy. Aembit ignores letter case in the scheme and host, a default port, and a trailing slash, and compares the path and query exactly.

### `redirect_uri` matching

`redirect_uri` must match a **Redirect URI** identifier on an active Client Workload in one of these ways:

* Exactly
* Against a configured value that ends in `/*`, with the same scheme, host, and port
* For `localhost` and `127.0.0.1`, with any port

See [Redirect URI](../client-workloads/identification/redirect-uri.md).

### `resource` matching

`resource` must be an absolute URI, or Aembit rejects the request with `invalid_target`. Aembit matches it to an active MCP Server Workload that meets these rules:

* The host matches, and can be a wildcard on the Server Workload
* The port and TLS setting match
* The path matches, unless the Server Workload’s **URL Path** is empty

## Consent at the MCP server

With an SSO Access Token Passthrough Credential Provider on the Gateway-to-Server Policy, Aembit skips this step and returns the authorization response directly.

With an MCP User-Based Access Token Credential Provider, Aembit sends the person to the MCP server’s authorization server on every run of the flow. It does so even when Aembit already holds a token for that person and MCP server. The `prompt` value Aembit sends depends on the **Authorization URL** of the MCP User-Based Access Token Credential Provider:

| When the Authorization URL contains | `prompt` value   | What Aembit asks the authorization server for                |
| ----------------------------------- | ---------------- | ------------------------------------------------------------ |
| `login.microsoftonline.com`         | `select_account` | An account picker                                            |
| `phrase.com`                        | None             | Nothing                                                      |
| Anything else                       | `consent`        | The consent page; the authorization server can still skip it |

## Authorization response

When the flow completes, Aembit redirects the browser to `redirect_uri` with these query parameters:

| Parameter | Value                                        |
| --------- | -------------------------------------------- |
| `code`    | The authorization code                       |
| `state`   | The `state` value from the authorize request |

## Token request and response

Your application exchanges the code at the token endpoint with a `POST` request that carries these form parameters.

| Parameter       | Value                                              |
| --------------- | -------------------------------------------------- |
| `grant_type`    | `authorization_code`                               |
| `code`          | The code from the authorization response           |
| `code_verifier` | The PKCE verifier that matches `code_challenge`    |
| `client_id`     | The same Aembit Client ID as the authorize request |
| `redirect_uri`  | The same `redirect_uri` as the authorize request   |

The response holds these fields:

| Field           | Value                                                                                        |
| --------------- | -------------------------------------------------------------------------------------------- |
| `access_token`  | An MCP Identity Gateway access token from the Client-to-Gateway Policy’s Credential Provider |
| `refresh_token` | Present when that Credential Provider has **Enable Refresh Token Support** turned on         |

With an OIDC ID Token Credential Provider, the access token carries the user identifier in its `idp_user_id` claim.

## Refresh request and response

Your application exchanges a refresh token at the token endpoint with a `POST` request that carries these form parameters.

| Parameter       | Value                                              |
| --------------- | -------------------------------------------------- |
| `grant_type`    | `refresh_token`                                    |
| `refresh_token` | The refresh token from the last token response     |
| `client_id`     | The same Aembit Client ID as the authorize request |

The response holds these fields:

| Field           | Value                                                |
| --------------- | ---------------------------------------------------- |
| `access_token`  | A new MCP Identity Gateway access token              |
| `refresh_token` | A new refresh token, which replaces the one you sent |

Each refresh token works once.

## Lifetimes

| Item                              | Lifetime                                                                     |
| --------------------------------- | ---------------------------------------------------------------------------- |
| Authorization code                | 5 minutes, and it works once                                                 |
| MCP Identity Gateway access token | The lifetime the Client-to-Gateway Policy’s Credential Provider sets         |
| Refresh token                     | Works once, until the Credential Provider’s **Absolute Token Lifetime** ends |

## User identification

Aembit takes the user identifier from the ID token that the Identity Provider returns:

| ID token contents                                | User identifier                                                 |
| ------------------------------------------------ | --------------------------------------------------------------- |
| A claim, such as `email`, holds an email address | That email address, in lowercase                                |
| No claim holds an email address                  | Another identifier claim, such as `sub` or `preferred_username` |
| None of these claims                             | None; the flow fails with `access_denied`                       |

Aembit expects the user identifier to be an email address, so configure your Identity Provider to include the person’s email address in the ID token.

## Errors

Errors that come before Aembit trusts `redirect_uri` do not reach your application. Aembit returns HTTP 400 or shows an Aembit error page in the browser instead, for any of these conditions:

* `client_id` is not a valid Aembit Client ID, names another Aembit Tenant, or names an inactive Identity Provider.
* `redirect_uri` matches no **Redirect URI** identifier (`invalid_redirect_uri`).
* `mcp_server_url` breaks a rule (`invalid_request`).

Errors after that point redirect the browser to `redirect_uri` with `error`, `error_description`, and the original `state`.

Aembit returns `error=access_denied` for each of these conditions, with the `error_description` that its row shows:

| Condition                                                                                                                                  | `error_description`                                                                                     |
| ------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------- |
| The Client-to-Gateway Policy fails                                                                                                         | `Aembit Access Policy validation failed`, followed by an authorization event ID                         |
| The Client Workload’s **Enforce SSO** list holds more than one active Identity Provider                                                    | `Multiple Identity Providers matched for Zero-UI authorization`                                         |
| The Client Workload’s **Enforce SSO** list holds no active Identity Provider                                                               | `No active Identity Provider found for Zero-UI authorization`                                           |
| The ID token holds no user identifier                                                                                                      | `Unable to determine user identity from identity provider.`                                             |
| No Gateway-to-Server Policy matches `mcp_server_url`                                                                                       | `No access policy found for the specified MCP server.`                                                  |
| More than one Gateway-to-Server Policy matches `mcp_server_url`                                                                            | `Multiple conflicting access policies found for the specified MCP server.`                              |
| The matched Access Policy does not have exactly one active MCP User-Based Access Token or SSO Access Token Passthrough Credential Provider | `The access policy for this MCP server is not properly configured.`                                     |
| The person declines consent at the MCP server                                                                                              | `Failed to authorize credential provider: User declined consent or no authorization code was provided.` |

When the MCP server’s authorization server returns an error, Aembit passes it to your application as follows:

| The authorization server returns                                             | `error`           | `error_description`                                                            |
| ---------------------------------------------------------------------------- | ----------------- | ------------------------------------------------------------------------------ |
| A standard OAuth error, such as `invalid_scope` or `temporarily_unavailable` | The same error    | Its `error_description`, or `Upstream authorization failed` when it sends none |
| A nonstandard error that names a redirect URI                                | `invalid_request` | `<upstream-error>: <upstream-description>`                                     |
| A nonstandard error that reports a denial                                    | `access_denied`   | `<upstream-error>: <upstream-description>`                                     |
| Any other nonstandard error                                                  | `server_error`    | `<upstream-error>: <upstream-description>`                                     |

When Aembit has no safe redirect for an error from the MCP server’s authorization server, it returns HTTP 400 with a JSON body instead.

At the token endpoint, a wrong `code_verifier` or a code that was already used returns `invalid_grant`. When the Client-to-Gateway Policy’s Credential Provider is an MCP User-Based Access Token, the token request returns `invalid_request`.

## Events

Aembit records an access authorization event for the Client-to-Gateway Policy when it evaluates the authorize request. The event ID in an `Aembit Access Policy validation failed` error points to that event. See [Audit and report on Workload activity](../../audit-report/overview.md).
