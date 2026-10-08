---
type: reference
title: "MCP Identity Gateway environment variables (self-hosted only)"
description: "Environment variables for configuring a self-hosted MCP Identity Gateway."
resource: https://docs.aembit.io/user-guide/deploy-install/mcp-identity-gateway/env-vars-mcp-gateway/
interface: mcp
tags: ["mcp-identity-gateway", "deploy-install"]
timestamp: 2026-10-08T13:12:21-07:00
---

# MCP Identity Gateway environment variables (self-hosted only)

This page is the configuration reference for [self-hosting the MCP Identity Gateway](self-host-mcp-gateway.md). You supply most of these environment variables on the install command when you run MCP Identity Gateway on your own host, and they configure how it operates at install time. [`AEMBIT_MCP_GATEWAY_TIMEOUT`](#aembit_mcp_gateway_timeout) is the one exception, and its entry explains how to set it.

> **Self-hosted only**
>
> These variables apply only to self-hosted deployments.

If you use the Aembit-managed service, you don’t configure any of these. Aembit sets them when it provisions your MCP Identity Gateway endpoint.

For Tenant-side configuration (Identity Provider, Trust Provider, and Access Policies), which applies to both deployment models, see [Set up the MCP Identity Gateway](../../access-policies/mcp-identity-gateway/setup-mcp-gateway.md).

## Required variables

### `AEMBIT_AUTHORIZATION_SERVER` Required

Default - not set

URL of the Aembit authorization server for this Aembit Tenant. The format is `https://<tenantId>.mcp.<region>.aembit.io/`.

Replace `<tenantId>` with your Aembit Tenant ID, visible in the Aembit Tenant URL (for example, `abc123` in `https://abc123.aembit.io`). Aembit Tenants use the `useast2` region. In the future, Aembit may add more regions, making this value dynamic.

See [Set up the MCP Identity Gateway](../../access-policies/mcp-identity-gateway/setup-mcp-gateway.md) for detailed configuration steps.

*Example*:\
`https://abc123.mcp.useast2.aembit.io/`

***

### `AEMBIT_MCP_GATEWAY_URL` Required

Default - not set

Public URL of this MCP Identity Gateway instance. This is the URL that MCP clients use to connect.

*Example*:\
`https://mcp-gateway.example.com`

***

### `AEMBIT_AGENT_CONTROLLER_URL` Required

Default - not set

URL of the local Agent Controller. Must point to localhost because the MCP Identity Gateway and Agent Controller must run on the same host. The Agent Controller registers the MCP Identity Gateway with Aembit Cloud and provides it with the credentials and configuration needed to operate. This local-only communication ensures credentials never traverse the network. For architecture details, see [MCP Identity Gateway concepts](concepts-mcp-gateway.md).

*Example*:\
`http://localhost:5000`

***

### `AEMBIT_TLS_CERT_CHAIN_PATH` Required

Default - not set

Sensitive - No

Filesystem path to the TLS certificate chain file (PEM format). Must include the full chain (leaf certificate and intermediates). The certificate Common Name (CN) or Subject Alternative Name (SAN) must match the hostname in `AEMBIT_MCP_GATEWAY_URL`.

*Example*:\
`/etc/ssl/certs/fullchain.pem`

***

### `AEMBIT_TLS_PRIVATE_KEY_PATH` Required

Default - not set

Sensitive - Yes

Filesystem path to the TLS private key file (PEM format). Store in a secrets manager for production.

*Example*:\
`/etc/ssl/private/privkey.pem`

***

## Optional variables

### `AEMBIT_LOG_LEVEL`

Default - `info`

Log verbosity level. Options: `trace`, `debug`, `info`, `warn`, `error`, `off`. Use `info` or higher for production; `trace` and `debug` may log sensitive information.

*Example*:\
`debug`

***

### `AEMBIT_MCP_GATEWAY_TIMEOUT`

Default - not set

Overrides how long the MCP Identity Gateway waits for your assigned MCP servers when it fans a request out to them. It also tunes the fan-out cap, upstream calls, upstream session caching, and Multi Round-Trip Request backoff.

> **Not supplied on the install command**
>
> Unlike the other variables on this page, the installer doesn’t pass this one through to the running service. The systemd unit it writes calls MCP Identity Gateway with a fixed set of arguments. The service starts with the default timeouts even when you set this variable on the install command. Use a systemd drop-in instead, as shown in the following example.

Most deployments never need this. The defaults are high enough that an AI client typically reaches its own timeout before MCP Identity Gateway reaches one of these. Treat it as a last resort for MCP servers whose responses exceed the default timeouts.

To override a timeout, create `/etc/systemd/system/aembit_mcp_gateway.service.d/override.conf`:

```ini
[Service]
Environment="AEMBIT_MCP_GATEWAY_TIMEOUT=tools/list=10s,resources/list=10s"
```

Then reload systemd and restart MCP Identity Gateway:

```shell
sudo systemctl daemon-reload
sudo systemctl restart aembit_mcp_gateway
```

A drop-in survives an upgrade. Edits to `/etc/systemd/system/aembit_mcp_gateway.service` don’t, because the installer replaces that file each time it runs.

The value is a comma-separated list of `<name>=<duration>` pairs. Durations take a unit suffix, such as `500ms`, `3s`, or `1m`. Any name you leave out keeps its default. MCP Identity Gateway validates the value when the service starts, and a zero duration or the same name listed twice stops the service from starting. A malformed value also fails the install command, even though a valid one has no effect there.

| Name             | Also accepted    | Default | Applies to                                                                                                        |
| ---------------- | ---------------- | ------- | ----------------------------------------------------------------------------------------------------------------- |
| `tools_list`     | `tools/list`     | `3s`    | The `tools/list` fanout for a client request, and the `tools/list` sent to each MCP server when a client connects |
| `resources_list` | `resources/list` | `3s`    | The `resources/list` fanout                                                                                       |
| `prompts_list`   | `prompts/list`   | `3s`    | The `prompts/list` fanout                                                                                         |

The following names tune the fan-out cap, upstream calls, upstream sessions, and Multi Round-Trip Request backoff.

| Name                            | Also accepted            | Default | Applies to                                                                                                                                                                                                                                              |
| ------------------------------- | ------------------------ | ------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `fanout_max`                    | `fanout/max`             | `30s`   | Upper limit on a fan-out timeout that MCP Identity Gateway extends to leave time for CrowdStrike AIDR. MCP Identity Gateway keeps a base timeout you set higher than this.                                                                              |
| `upstream_call_timeout`         | `upstream/call`          | `30s`   | Upper bound on each upstream operation, including session connect and each MCP call.                                                                                                                                                                    |
| `upstream_session_ttl`          | `upstream/session/ttl`   | `15m`   | How long an idle upstream session survives before MCP Identity Gateway drops it.                                                                                                                                                                        |
| `upstream_session_purge_period` | `upstream/session/purge` | `60s`   | How often MCP Identity Gateway checks for and removes idle upstream sessions past `upstream_session_ttl`.                                                                                                                                               |
| `mrtr_backoff_base`             | -                        | `50ms`  | Initial backoff delay between consecutive rounds of a Multi Round-Trip Request that ask for no input, for a client using a revision earlier than 2026-07-28. Doubles each such round, up to `mrtr_backoff_max`, and resets when a round asks for input. |
| `mrtr_backoff_max`              | -                        | `250ms` | Ceiling for the `mrtr_backoff_base` backoff delay.                                                                                                                                                                                                      |

The following names have no effect in MCP Identity Gateway 1.34.6034 and later. MCP Identity Gateway still accepts them, so a value that sets one doesn’t stop the service from starting. `upstream_call_timeout` and the list timeouts now bound the upstream calls they covered.

| Name                               | Also accepted                      |
| ---------------------------------- | ---------------------------------- |
| `initialize`                       | -                                  |
| `notifications_initialized`        | `notifications/initialized`        |
| `proactive_tools_list`             | -                                  |
| `reinit_initialize`                | -                                  |
| `reinit_notifications_initialized` | `reinit/notifications/initialized` |
| `reinit_tools_list`                | `reinit/tools/list`                |

*Example*:\
`tools/list=10s,resources/list=10s`

***

### `AEMBIT_MCP_SESSION_IDLE_TTL_SECS`

Default - `43200` (12 hours)

How long the MCP Identity Gateway keeps an MCP session that receives no requests. Every request on a session refreshes its expiry. When the window passes, MCP Identity Gateway drops the session, and the client’s next request returns `404 Not Found` so the client starts a new session.

The maximum is `1209600` seconds (14 days). A larger value fails the install.

This variable applies whether MCP Identity Gateway keeps sessions in memory or in Valkey. See [Session persistence](session-persistence-mcp-gateway.md).

*Example*:\
`86400`

***

### `AEMBIT_METRICS_PORT`

Default - `9091`

Port number for the Prometheus-compatible metrics endpoint. The MCP Identity Gateway exposes metrics at `/metrics` on this port.

Port `9091` avoids a collision with the Agent Controller, which uses port `9090` for its own metrics endpoint on the same host.

*Example*:\
`9092`

***

### `AEMBIT_TRUSTED_ISSUER_DOMAINS`

Default - not set

Additional trusted issuer domains for token validation. When set, MCP Identity Gateway also accepts tokens from these domains beyond the default Aembit Cloud domain.

This variable is primarily for testing and development environments where MCP Identity Gateway needs to work with non-production Aembit Cloud instances or mocked services. Most production deployments don’t need this variable.

*Example*:\
`issuer.example.com`

***

### `AEMBIT_VALKEY_URL`

Default - not set

Sensitive - Yes

URL of a Valkey instance to store MCP sessions in. When you leave this variable unset, the MCP Identity Gateway keeps sessions in process memory, and a restart ends every open session. Setting it lets sessions survive a restart and lets more than one MCP Identity Gateway instance share session state.

MCP Identity Gateway accepts `redis://<host>[:<port>]` and `rediss://<host>[:<port>]`. Use `rediss://` for any network connection, because `redis://` sends session data unencrypted.

You can include Valkey credentials as `rediss://<user>:<password>@<host>` or as `user` and `pass` query parameters, which is why this value is sensitive. MCP Identity Gateway redacts them when it logs the URL at startup.

The MCP Identity Gateway connects to Valkey while it validates your install arguments and again when the service starts. It fails closed on both: an unreachable Valkey stops the install and stops the service.

For what a session holds and how to operate the store, see [Session persistence](session-persistence-mcp-gateway.md).

*Example*:\
`rediss://valkey.internal.example.com:6379`

## Example installation

> **Production security**
>
> The following example shows environment variables on the command line for clarity. In production, use an environment file with restricted permissions (`chmod 600`) or inject values from a secrets manager so configuration values don’t appear in shell history or process listings.

```shell
sudo AEMBIT_AUTHORIZATION_SERVER=https://abc123.mcp.useast2.aembit.io/ \
     AEMBIT_MCP_GATEWAY_URL=https://mcp-gateway.example.com \
     AEMBIT_TLS_CERT_CHAIN_PATH=/etc/ssl/certs/fullchain.pem \
     AEMBIT_TLS_PRIVATE_KEY_PATH=/etc/ssl/private/privkey.pem \
     AEMBIT_AGENT_CONTROLLER_URL=http://localhost:5000 \
     AEMBIT_LOG_LEVEL=info \
     ./install
```

## Validation and failure behavior

The MCP Identity Gateway validates configuration at startup and fails closed on errors.

### Startup validation

When the MCP Identity Gateway starts, it validates:

* **Required variables** - You must set all required environment variables
* **URL format** - Authorization server and controller URLs must be well-formed
* **TLS certificates** - Certificate and key files must exist and be readable
* **Agent Controller connectivity** - The MCP Identity Gateway must reach the Agent Controller on localhost

If any validation fails, the MCP Identity Gateway exits immediately with an error message.

### Runtime caching

During operation, the MCP Identity Gateway caches data from Aembit Cloud to maintain availability:

| Data type                     | Cache duration |
| ----------------------------- | -------------- |
| Assigned MCP server workloads | 60 seconds     |
| Access policy directives      | 60 seconds     |
| Credentials                   | 60 seconds     |

If Aembit Cloud becomes unreachable, the MCP Identity Gateway continues operating with cached data until the cache expires.

### Verifying configuration

After installation, verify the MCP Identity Gateway is running:

```shell
sudo systemctl status aembit_mcp_gateway
```

Check the logs for startup errors:

```shell
sudo journalctl -u aembit_mcp_gateway -n 50
```

## Agent Controller environment variables

The MCP Identity Gateway requires an Agent Controller running on the same host. The Agent Controller uses its own set of environment variables during installation.

### `AEMBIT_TENANT_ID` Required

Default - not set

Aembit Tenant ID.

*Example*:\
`123abc`

***

### `AEMBIT_AGENT_CONTROLLER_ID` Required

Default - not set

ID of the Agent Controller as configured in Aembit.

*Example*:\
`01234567-89ab-cdef-0123-456789abcdef`

***

### `AEMBIT_STACK_DOMAIN`

Default - `useast2.aembit.io`

Aembit stack domain for this tenant. **Don’t set this value unless directed by your Aembit representative.**

***

### `AEMBIT_LOG_LEVEL`

Default - `information`

Log verbosity level for Agent Controller. The supported levels include `fatal`, `error`, `warning`, `information`, `debug`, `verbose`.

*Example*:\
`verbose`

> **Log level mapping**
>
> The Agent Controller uses its own `AEMBIT_LOG_LEVEL` independently from MCP Identity Gateway. For consistent logging in aggregated environments, use the following mapping:
>
> | MCP Identity Gateway level | Agent Controller level |
> | -------------------------- | ---------------------- |
> | `info`                     | `information`          |
> | `debug`                    | `debug`                |
> | `trace`                    | `verbose`              |

For Agent Controller installation details, see [Set up the MCP Identity Gateway](../../access-policies/mcp-identity-gateway/setup-mcp-gateway.md).

## Related resources

* [MCP Identity Gateway reference](reference-mcp-gateway.md)
