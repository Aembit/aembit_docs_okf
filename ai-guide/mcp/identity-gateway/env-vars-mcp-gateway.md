---
type: reference
title: "MCP Identity Gateway environment variables (self-hosted only)"
description: "Environment variables for configuring a self-hosted MCP Identity Gateway."
resource: https://docs.aembit.io/ai-guide/mcp/identity-gateway/env-vars-mcp-gateway/
interface: mcp
tags: [identity-gateway, mcp]
timestamp: 2026-06-23T16:02:22-07:00
---

# MCP Identity Gateway environment variables (self-hosted only)

This page is the configuration reference for [self-hosting the MCP Identity Gateway](self-host-mcp-gateway.md). You supply these environment variables on the install command when you run the Gateway on your own host, and they configure how it operates at install time.

Self-hosted only

These variables apply only to self-hosted deployments.

If you use the Aembit-managed service, you don’t configure any of these. Aembit sets them when it provisions your Gateway endpoint.

For Tenant-side configuration (Identity Provider, Trust Provider, and Access Policies), which applies to both deployment models, see [Set up the MCP Identity Gateway](setup-mcp-gateway.md).

## Required variables

[Section titled “Required variables”](#required-variables)

### `AEMBIT_AUTHORIZATION_SERVER` Required

[Section titled “AEMBIT\_AUTHORIZATION\_SERVER ”](#aembit_authorization_server)

Default - not set

URL of the Aembit authorization server for this Aembit Tenant. The format is `https://<tenantId>.mcp.<region>.aembit.io/`.

Replace `<tenantId>` with your Aembit Tenant ID, visible in the Aembit Tenant URL (for example, `abc123` in `https://abc123.aembit.io`). Aembit Tenants use the `useast2` region. In the future, Aembit may add more regions, making this value dynamic.

See [Set up the MCP Identity Gateway](setup-mcp-gateway.md) for detailed configuration steps.

*Example*:\
`https://abc123.mcp.useast2.aembit.io/`

***

### `AEMBIT_MCP_GATEWAY_URL` Required

[Section titled “AEMBIT\_MCP\_GATEWAY\_URL ”](#aembit_mcp_gateway_url)

Default - not set

Public URL of this MCP Identity Gateway instance. This is the URL that MCP clients use to connect.

*Example*:\
`https://mcp-gateway.example.com`

***

### `AEMBIT_AGENT_CONTROLLER_URL` Required

[Section titled “AEMBIT\_AGENT\_CONTROLLER\_URL ”](#aembit_agent_controller_url)

Default - not set

URL of the local Agent Controller. Must point to localhost because the MCP Identity Gateway and Agent Controller must run on the same host. The Agent Controller registers the MCP Identity Gateway with Aembit Cloud and provides it with the credentials and configuration needed to operate. This local-only communication ensures credentials never traverse the network. For architecture details, see [MCP Identity Gateway concepts](concepts-mcp-gateway.md).

*Example*:\
`http://localhost:5000`

***

### `AEMBIT_TLS_CERT_CHAIN_PATH` Required

[Section titled “AEMBIT\_TLS\_CERT\_CHAIN\_PATH ”](#aembit_tls_cert_chain_path)

Default - not set

Sensitive - No

Filesystem path to the TLS certificate chain file (PEM format). Must include the full chain (leaf certificate and intermediates). The certificate Common Name (CN) or Subject Alternative Name (SAN) must match the hostname in `AEMBIT_MCP_GATEWAY_URL`.

*Example*:\
`/etc/ssl/certs/fullchain.pem`

***

### `AEMBIT_TLS_PRIVATE_KEY_PATH` Required

[Section titled “AEMBIT\_TLS\_PRIVATE\_KEY\_PATH ”](#aembit_tls_private_key_path)

Default - not set

Sensitive - Yes

Filesystem path to the TLS private key file (PEM format). Store in a secrets manager for production.

*Example*:\
`/etc/ssl/private/privkey.pem`

***

## Optional variables

[Section titled “Optional variables”](#optional-variables)

### `AEMBIT_LOG_LEVEL`

[Section titled “AEMBIT\_LOG\_LEVEL”](#aembit_log_level)

Default - `info`

Log verbosity level. Options: `trace`, `debug`, `info`, `warn`, `error`, `off`. Use `info` or higher for production; `trace` and `debug` may log sensitive information.

*Example*:\
`debug`

***

### `AEMBIT_METRICS_PORT`

[Section titled “AEMBIT\_METRICS\_PORT”](#aembit_metrics_port)

Default - `9091`

Port number for the Prometheus-compatible metrics endpoint. The MCP Identity Gateway exposes metrics at `/metrics` on this port.

Port `9091` avoids a collision with the Agent Controller, which uses port `9090` for its own metrics endpoint on the same host.

*Example*:\
`9092`

***

### `AEMBIT_TRUSTED_ISSUER_DOMAINS`

[Section titled “AEMBIT\_TRUSTED\_ISSUER\_DOMAINS”](#aembit_trusted_issuer_domains)

Default - not set

Additional trusted issuer domains for token validation. When set, MCP Identity Gateway also accepts tokens from these domains beyond the default Aembit Cloud domain.

This variable is primarily for testing and development environments where MCP Identity Gateway needs to work with non-production Aembit Cloud instances or mocked services. Most production deployments don’t need this variable.

*Example*:\
`test.aembit-eng.com`

## Example installation

[Section titled “Example installation”](#example-installation)

Production security

The following example shows environment variables on the command line for clarity. In production, use an environment file with restricted permissions (`chmod 600`) or inject values from a secrets manager so configuration values don’t appear in shell history or process listings.

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

[Section titled “Validation and failure behavior”](#validation-and-failure-behavior)

The MCP Identity Gateway validates configuration at startup and fails closed on errors.

### Startup validation

[Section titled “Startup validation”](#startup-validation)

When the MCP Identity Gateway starts, it validates:

* **Required variables** - You must set all required environment variables
* **URL format** - Authorization server and controller URLs must be well-formed
* **TLS certificates** - Certificate and key files must exist and be readable
* **Agent Controller connectivity** - The MCP Identity Gateway must reach the Agent Controller on localhost

If any validation fails, the MCP Identity Gateway exits immediately with an error message.

### Runtime caching

[Section titled “Runtime caching”](#runtime-caching)

During operation, the MCP Identity Gateway caches data from Aembit Cloud to maintain availability:

| Data type                     | Cache duration |
| ----------------------------- | -------------- |
| Assigned MCP server workloads | 60 seconds     |
| Access policy directives      | 60 seconds     |
| Credentials                   | 60 seconds     |

If Aembit Cloud becomes unreachable, the MCP Identity Gateway continues operating with cached data until the cache expires.

### Verifying configuration

[Section titled “Verifying configuration”](#verifying-configuration)

After installation, verify the MCP Identity Gateway is running:

```shell
sudo systemctl status aembit_mcp_gateway
```

Check the logs for startup errors:

```shell
sudo journalctl -u aembit_mcp_gateway -n 50
```

## Agent Controller environment variables

[Section titled “Agent Controller environment variables”](#agent-controller-environment-variables)

The MCP Identity Gateway requires an Agent Controller running on the same host. The Agent Controller uses its own set of environment variables during installation.

### `AEMBIT_TENANT_ID` Required

[Section titled “AEMBIT\_TENANT\_ID ”](#aembit_tenant_id)

Default - not set

Aembit Tenant ID.

*Example*:\
`123abc`

***

### `AEMBIT_AGENT_CONTROLLER_ID` Required

[Section titled “AEMBIT\_AGENT\_CONTROLLER\_ID ”](#aembit_agent_controller_id)

Default - not set

ID of the Agent Controller as configured in Aembit.

*Example*:\
`01234567-89ab-cdef-0123-456789abcdef`

***

### `AEMBIT_STACK_DOMAIN`

[Section titled “AEMBIT\_STACK\_DOMAIN”](#aembit_stack_domain)

Default - `useast2.aembit.io`

Aembit stack domain for this tenant. **Don’t set this value unless directed by your Aembit representative.**

***

### `AEMBIT_LOG_LEVEL`

[Section titled “AEMBIT\_LOG\_LEVEL”](#aembit_log_level-1)

Default - `information`

Log verbosity level for Agent Controller. The supported levels include `fatal`, `error`, `warning`, `information`, `debug`, `verbose`.

*Example*:\
`verbose`

Log level mapping

The Agent Controller uses its own `AEMBIT_LOG_LEVEL` independently from the Gateway. For consistent logging in aggregated environments, use the following mapping:

| Gateway level | Agent Controller level |
| ------------- | ---------------------- |
| `info`        | `information`          |
| `debug`       | `debug`                |
| `trace`       | `verbose`              |

For Agent Controller installation details, see [Set up the MCP Identity Gateway](setup-mcp-gateway.md).

## Related resources

[Section titled “Related resources”](#related-resources)

* [MCP Identity Gateway reference](reference-mcp-gateway.md)
