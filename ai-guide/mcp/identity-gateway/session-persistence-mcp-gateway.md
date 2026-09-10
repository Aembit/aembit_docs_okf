---
type: explanation
title: "Session persistence in the MCP Identity Gateway"
description: "How the MCP Identity Gateway stores MCP sessions, and how to persist them across restarts with Valkey."
resource: https://docs.aembit.io/ai-guide/mcp/identity-gateway/session-persistence-mcp-gateway/
tags: ["identity-gateway", "mcp"]
timestamp: 2026-08-19T15:11:05-07:00
---

# Session persistence in the MCP Identity Gateway

The MCP Identity Gateway holds a session for each MCP client that connects to it. By default it keeps those sessions in memory, so a restart ends every open session. A self-hosted Gateway can instead keep them in Valkey, which lets sessions survive a restart or an upgrade.

This page explains what a session holds, what happens to sessions when the Gateway restarts, and how to configure and operate a Valkey session store.

> **Self-hosted only**
>
> Session store configuration applies only to self-hosted deployments. The Aembit-managed service runs its own session store, which Aembit operates.

## What a session holds

An MCP session is protocol state. When a client sends `initialize`, the Gateway creates a session, returns a session ID, and the client sends that ID in the `Mcp-Session-Id` header on every later request. The session records which upstream MCP servers the Gateway has initialized for that client, the upstream session IDs those servers returned, and the client’s cached tool list.

A session isn’t authentication state. The Gateway validates the caller’s access token on every request and evaluates Access Policy decisions per request, so a persisted session never extends anyone’s access. Revoking access takes effect on the next request whether a session exists. For how the Gateway identifies the caller, see [Client workload identification](client-workload-identification.md).

The Gateway keys each session by Tenant, user, and session ID together, so only the user who created a session ID can use it.

## The default in-memory store

With no session store configured, the Gateway keeps sessions in process memory. This needs no external dependency and is the right choice for a single Gateway where an occasional reconnect is acceptable.

Sessions don’t survive the process. Restarting the service, as you do after a configuration change or an upgrade, ends every open session. Each client’s next request returns `404 Not Found`, and the client starts a new session. Clients that follow the MCP specification re-initialize on their own. The visible effect is a pause and a fresh handshake rather than an error the user has to act on.

An in-memory store is also per-process, so it can’t back more than one Gateway instance. Two instances behind a load balancer each see only their own sessions, and a client whose requests land on the other instance gets `404 Not Found`.

## Persist sessions with Valkey

Set `AEMBIT_VALKEY_URL` on the install command to store sessions in Valkey instead:

```shell
sudo AEMBIT_AUTHORIZATION_SERVER=https://abc123.mcp.useast2.aembit.io/ \
     AEMBIT_MCP_GATEWAY_URL=https://mcp-gateway.example.com \
     AEMBIT_TLS_CERT_CHAIN_PATH=/etc/ssl/certs/fullchain.pem \
     AEMBIT_TLS_PRIVATE_KEY_PATH=/etc/ssl/private/privkey.pem \
     AEMBIT_AGENT_CONTROLLER_URL=http://localhost:5000 \
     AEMBIT_VALKEY_URL=rediss://valkey.internal.example.com:6379 \
     ./install
```

The Gateway accepts these URL forms:

| Form                       | Transport    |
| -------------------------- | ------------ |
| `redis://<host>[:<port>]`  | TCP, no TLS  |
| `rediss://<host>[:<port>]` | TCP with TLS |

Use `rediss://` whenever the Gateway reaches Valkey over a network. The `redis://` scheme sends session data unencrypted, so reserve it for a loopback address.

You can supply Valkey credentials in the URL, either as `rediss://<user>:<password>@<host>` or as `user` and `pass` query parameters. Treat the whole value as a secret. Pass it from a secrets manager or an environment file with restricted permissions rather than typing it on the command line. The Gateway redacts these credentials when it logs the URL at startup.

Sessions live outside the process, so two or more Gateway instances pointed at the same Valkey share session state, and any instance can serve a given client.

For the full variable reference, see [`AEMBIT_VALKEY_URL`](env-vars-mcp-gateway.md#aembit_valkey_url).

## Session idle timeout

The Gateway expires a session that goes unused. `AEMBIT_MCP_SESSION_IDLE_TTL_SECS` sets that window, and it defaults to 12 hours. Every request on a session refreshes its expiry.

The value applies to both store types, and the maximum is 14 days (`1209600` seconds). A larger value fails the install.

Raising the timeout keeps idle clients from re-initializing, at the cost of more concurrent sessions held at once. Lowering it releases session state sooner.

## Operate Valkey

The Gateway treats Valkey as a dependency it doesn’t manage. A few operational choices matter more than the rest:

* **Manage Valkey independently of the Gateway.** The point of the session store is to outlive the Gateway process. Running Valkey where a Gateway upgrade can restart or replace it removes that benefit.
* **Require TLS and authentication.** Session records name the Tenant, the user, and the upstream servers a client has open.
* **Decide whether session loss is acceptable.** Sessions are recoverable state, so Valkey persistence to disk is optional. Enable it when you want sessions to survive a Valkey restart as well as a Gateway restart.
* **Keep Valkey reachable.** The Gateway doesn’t fall back to its in-memory store if Valkey becomes unreachable, so plan for the same availability you expect from the Gateway itself.

The Gateway stores each session under a key of the form `aembit_mcp_gateway:tenant:<tenantId>:user:<userId>:session:<sessionId>`. Don’t point another application at the same Valkey database.

## Failure behavior

**At startup**, the Gateway fails closed. When you set `AEMBIT_VALKEY_URL`, the installer connects to Valkey as part of validating your arguments, and the Gateway connects again when the service starts. If either connection fails, the Gateway doesn’t start, and it reports the reason.

**During operation**, each session store operation retries up to three times, backing off 100 ms and then 200 ms, which adds at most 300 ms before the operation gives up. Errors that aren’t connection failures fail immediately without retrying.

If Valkey stays unreachable after those retries, the Gateway keeps serving `initialize` and keeps issuing session IDs, but it can’t save or read the sessions behind them. Requests that carry a session ID return `404 Not Found`, so clients re-initialize and then fail again on their next request until Valkey recovers. The Gateway logs each failed operation.

A session record the Gateway can’t read—because it’s corrupted, or because a newer Gateway build wrote it—counts as a miss rather than an error. The Gateway re-initializes the session instead of failing the request.

When you configure Valkey, its health also gates the Gateway’s readiness endpoint, so an unready Gateway leaves the load balancer rotation while Valkey is down. The in-memory store never gates readiness.

## Monitor the session store

Two metrics cover session store activity, both labeled with `operation` (`save`, `get`, `delete`, or `count`), `outcome` (`success` or `error`), and `reason` (`none`, `not_found`, or `store_error`):

* `aembit_mcp_gateway_session_store_operations_total`
* `aembit_mcp_gateway_session_store_operation_duration_seconds`

A rising rate of `outcome="error"` with `reason="store_error"` is the signal that Valkey is failing, and the duration histogram shows the latency the store adds to requests. `aembit_mcp_gateway_readiness_probe_flaps_total` with `probe="session_store"` counts how often the store has taken the Gateway out of rotation.

`aembit_mcp_gateway_sessions_active` reports open sessions per Tenant. The Gateway recomputes it from the session store on an interval, so it can lag the true count.

For every metric and label, see [MCP Identity Gateway metrics](reference-mcp-gateway.md#prometheus-metrics).

## Troubleshoot

Check the service logs first:

```shell
sudo journalctl -u aembit_mcp_gateway -n 50
```

At startup the Gateway logs which store it chose, so that line confirms whether it read your URL at all.

Two connection failures have specific causes:

* **A TLS scheme mismatch.** The Gateway reports receiving TLS data on a non-TLS connection when the URL uses `redis://` against a TLS-enabled Valkey. Switch the scheme to `rediss://`.
* **A rejected connection.** The Gateway reports a broken pipe when Valkey closes the connection without answering. Valkey protected mode does this: it rejects connections from addresses other than loopback until you configure a password or bind address. Either allow the Gateway’s address or turn protected mode off.

If clients get `404 Not Found` on every request after a successful `initialize`, the Gateway is reaching Valkey at startup but failing on session reads and writes. Check the session store metrics for `reason="store_error"` and confirm Valkey is still reachable from the Gateway host.

## Related resources

* [MCP Identity Gateway environment variables](env-vars-mcp-gateway.md)
* [Self-host the MCP Identity Gateway](self-host-mcp-gateway.md)
* [MCP Identity Gateway reference](reference-mcp-gateway.md#session-management)
