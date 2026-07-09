---
type: reference
title: "MCP server environment variables (for Aembit MCP Authorization Server)"
description: "Reference for environment variables to configure MCP servers to use the Aembit MCP Authorization Server"
resource: https://docs.aembit.io/ai-guide/mcp/authorization-server/env-vars-mcp-auth-server/
interface: mcp
tags: [authorization-server, mcp]
timestamp: 2026-06-18T13:15:52-04:00
---

# MCP server environment variables (for Aembit MCP Authorization Server)

These environment variables configure your MCP server (resource server), so it can use the Aembit MCP Authorization Server. Use these environment variables to configure your MCP server and **not** the Aembit-hosted MCP Authorization Server.

## MCP server environment variables

[Section titled “MCP server environment variables”](#mcp-server-environment-variables)

The following environment variables configure your MCP server to work with the Aembit MCP Authorization Server.

### `MCP_SERVER_HOST`

[Section titled “MCP\_SERVER\_HOST”](#mcp_server_host)

Default - `0.0.0.0`

The network interface address your MCP server binds to.

*Example*:\
`0.0.0.0`

***

### `MCP_SERVER_PORT`

[Section titled “MCP\_SERVER\_PORT”](#mcp_server_port)

Default - `8000`

The port your MCP server listens on.

*Example*:\
`8000`

***

### `MCP_SERVER_URL` Required

[Section titled “MCP\_SERVER\_URL ”](#mcp_server_url)

Default - not set

The public URL of your MCP server. OAuth callbacks and token audience validation use this URL. It must match the URL that MCP clients use to connect to your server and the Server Workload configuration in Aembit.

See [URL configuration alignment](concepts-mcp-auth-server.md#url-configuration-alignment) for details on ensuring your URLs match correctly.

*Example*:\
`http://localhost:8000`

***

### `AEMBIT_MCP_AUTH_SERVER` Required

[Section titled “AEMBIT\_MCP\_AUTH\_SERVER ”](#aembit_mcp_auth_server)

Default - not set

The URL of the Aembit MCP Authorization Server for your tenant. This URL uses the `.mcp.` subdomain.

You can find this URL in the **Aembit MCP Authorization Server URL** field when you configure a Server Workload with the MCP application protocol.

*Example*:\
`https://abc123.mcp.useast2.aembit.io`

***

### `AEMBIT_ISSUER` Required

[Section titled “AEMBIT\_ISSUER ”](#aembit_issuer)

Default - not set

The token issuer URL used during JWT verification. This URL uses the `.id.` subdomain, **not** the `.mcp.` subdomain.

Caution

The issuer URL must use the `.id.` subdomain (for example, `abc123.id.useast2.aembit.io`). Using the `.mcp.` subdomain causes token verification to fail.

See [Tenant URL patterns](reference-mcp-auth-server.md#tenant-url-patterns) for details on Aembit subdomain usage.

*Example*:\
`https://abc123.id.useast2.aembit.io`

***

### `AEMBIT_JWKS_URI` Required

[Section titled “AEMBIT\_JWKS\_URI ”](#aembit_jwks_uri)

Default - not set

The JSON Web Key Set (JWKS) endpoint for token signature verification. Your MCP server uses this endpoint to retrieve the public keys needed to validate access tokens issued by the Aembit MCP Authorization Server.

*Example*:\
`https://abc123.mcp.useast2.aembit.io/.well-known/openid-configuration/jwks`

## Related resources

[Section titled “Related resources”](#related-resources)

For an example of how to use these environment variables in a Python MCP server, see [Test with a demo MCP server](setup-mcp-auth-server.md#test-with-a-demo-mcp-server) in the setup guide.

* [MCP Authorization Server reference](reference-mcp-auth-server.md)
* [Set up the MCP Authorization Server](setup-mcp-auth-server.md)
* [Tenant URL patterns](reference-mcp-auth-server.md#tenant-url-patterns)
