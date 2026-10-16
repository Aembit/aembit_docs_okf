---
type: reference
title: "Edge SDK logging and network activity"
description: "What the Edge SDKs log, how to send those events to your logger, and which hosts the SDKs contact."
resource: https://docs.aembit.io/dev-guide/sdk/edge/logging/
interface: sdk
tags: ["edge", "sdk"]
timestamp: 2026-10-15T13:04:22-07:00
---

# Edge SDK logging and network activity

The Edge SDKs log events that trace authentication, identity collection, token caching, and credential retrieval. The events carry identifiers, expiry times, and error messages. The SDKs never add tokens or credential values to them, though an error message can carry text from outside the SDK. Nothing appears until your application sends the events to a logger. The SDKs send no telemetry, and they contact only the [Aembit Edge API](../../api/edge/overview.md) and the identity source your Trust Provider reads.

## Send SDK events to your logger

* TypeScript

  Pass a `logger` when you construct the `EdgeClient`. Without one, the TypeScript SDK logs nothing. A logger is any object with `debug`, `info`, `warn`, and `error` methods that take a message and an optional context object, so `console` works as is.

  **edge-client.ts**

  ```typescript
  import { EdgeClient, trustProviders } from "@aembit/edge-sdk";


  const client = new EdgeClient({
    baseUrl: "https://<tenant>.ec.<stack>.aembit.io",
    clientId: "your-edge-sdk-client-id",
    trustProvider: trustProviders.oidcIdToken({
      identityToken: "your-oidc-id-token",
    }),
    logger: console,
  });
  ```

  The SDK skips any of the four methods your logger lacks, and it ignores exceptions those methods throw, so a logging method that throws never fails an SDK call. To use another logging library, pass an object whose four methods call that library. The [TypeScript logging example](https://github.com/Aembit/edge-sdks/tree/main/ts/examples/logging_integration) shows a structured JSON adapter.

* Python

  The Python SDK logs through the standard `logging` module, under the `aembit_edge` logger. The SDK attaches a `NullHandler` to that logger, so an application that configures no logging gets no SDK output. Once your application configures logging, SDK events reach its handlers like any library’s events. At the default `WARNING` level, only the failure events appear.

  Set the `aembit_edge` logger’s level to see the rest:

  **logging\_setup.py**

  ```python
  import logging


  logging.basicConfig(level=logging.INFO)
  logging.getLogger("aembit_edge").setLevel(logging.DEBUG)
  ```

  > **Raise only the `aembit_edge` logger to `DEBUG`**
  >
  > The AWS Role Trust Provider signs its identity request with botocore, which logs each request it signs at `DEBUG` level. With temporary AWS credentials, that logged request includes the AWS session token. A root logger at `DEBUG` writes the session token to your logs, so set `DEBUG` on `aembit_edge` alone.

  The [Python logging examples](https://github.com/Aembit/edge-sdks/tree/main/py/examples/logging_integration) show the standard library and a third-party logger.

The SDKs have no log level or redaction setting of their own. Filter events in your logger.

## Log events

Both SDKs log these events with the same messages. The TypeScript SDK passes each event’s fields as the context object. The Python SDK writes them into the message text, in parentheses.

| Message                                             | Level   | TypeScript fields                        | Python fields                                |
| --------------------------------------------------- | ------- | ---------------------------------------- | -------------------------------------------- |
| `EdgeClient initialized`                            | `debug` | `baseUrl`, `clientId`, `trustProviderId` | `base_url`, `client_id`, `trust_provider_id` |
| `Authenticating workload`                           | `info`  | `clientId`, `trustProviderId`            | `clientId`, `trustProviderId`                |
| `Workload authenticated successfully`               | `info`  | `trustProviderId`, `expiresAt`           | `trustProviderId`, `expiresAt`               |
| `Collecting identity from Trust Provider`           | `debug` | `trustProviderId`                        | `trustProviderId`                            |
| `Identity collected from Trust Provider`            | `debug` | `trustProviderId`, `authCacheKey`        | `trustProviderId`, `authCacheKey`            |
| `Trust Provider failed to collect identity`         | `error` | `trustProviderId`, `error`               | `trustProviderId`, `error`                   |
| `Reusing valid cached access token`                 | `debug` | `authCacheKey`, `resourceSet`            | `authCacheKey`, `resourceSet`                |
| `Acquiring new access token`                        | `debug` | none                                     | none                                         |
| `Sending authentication request to Edge API`        | `debug` | `clientId`, `resourceSet`                | none                                         |
| `Authentication response received and token cached` | `debug` | `expiresAtMs`                            | `expires_at_ms`                              |
| `Authentication request failed`                     | `error` | `clientId`, `error`                      | `clientId`, `error`                          |
| `Retrieving credential`                             | `info`  | `server`, `credentialType`               | `server`, `credentialType`                   |
| `Credential retrieved successfully`                 | `info`  | `credentialType`, `expiresAt`            | `credentialType`, `expiresAt`                |
| `Credential retrieval failed`                       | `error` | `server`, `error`                        | `server`, `error`                            |

The fields hold these values:

* `clientId` is the Edge SDK Client ID, and `baseUrl` is the Edge API base URL, which contains your tenant ID.
* `server` is the host and port of the service the credential is for, written as `host:port`.
* `resourceSet` is the Resource Set ID the request carries, when you set one. On `Sending authentication request to Edge API`, the TypeScript SDK logs only a Resource Set passed to `getCredential()`, not one set on the client.
* `authCacheKey` identifies the cached access token. The TypeScript SDK’s token-based Trust Providers set it to a provider prefix and a SHA-256 hash of the identity token. Every other built-in Trust Provider leaves it empty.
* `error` is the error’s message. When the Edge API returns an error, the message comes from the response body or names the HTTP status.

The Python SDK logs each failure event with its stack trace. The TypeScript SDK also logs an `error` event, starting with `getCredential() failed:`, when `getCredential()` receives an input that’s `null` or not an object, or an input with no `server` key. The same happens when you pass options that are `null`, or a value other than an object or `undefined`.

## What the logs leave out

The SDKs never log an access token or the data of a credential, such as an API key or a password. They also never log the identity evidence a built-in Trust Provider collects, such as an OpenID Connect (OIDC) ID token or an instance identity document. In Python, botocore logs the AWS Role Trust Provider’s signed request at `DEBUG` level under its own logger, as [Send SDK events to your logger](#send-sdk-events-to-your-logger) describes.

The SDKs don’t redact the `error` field, which holds the message of the error that caused the failure. That message can include text from outside the SDK. Examples are the error message the Edge API returns, an HTTP library error, and an error your token function or a custom Trust Provider throws. The Python stack trace also includes the messages of chained exceptions. Keep tokens out of the errors your own token functions and Trust Providers throw.

## Network activity

The SDKs send no telemetry or usage data. They set no `User-Agent` header of their own.

| Destination                                                        | Trust Providers                 | When                                       |
| ------------------------------------------------------------------ | ------------------------------- | ------------------------------------------ |
| The Edge API at your `baseUrl`                                     | All                             | Each authentication and credential request |
| AWS Instance Metadata Service (IMDS), `169.254.169.254` by default | AWS Metadata Service            | Each identity collection                   |
| Azure IMDS, `169.254.169.254` by default                           | Azure Instance Metadata Service | Each identity collection                   |
| The sources in the AWS default credential chain                    | AWS Role                        | When the SDK resolves AWS credentials      |

The AWS Role Trust Provider signs its identity request locally and sends it to the Edge API, not to AWS. The other Trust Providers make no network call. They take the identity token from your application, except the Python Kubernetes Trust Provider, which reads the service account token file when you pass no token.
