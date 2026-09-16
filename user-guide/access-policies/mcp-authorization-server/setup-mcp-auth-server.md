---
type: how-to
title: "Set up the MCP Authorization Server"
description: "How to configure Access Policies and register MCP clients for the Aembit MCP Authorization Server."
resource: https://docs.aembit.io/user-guide/access-policies/mcp-authorization-server/setup-mcp-auth-server/
interface: mcp
tags: ["mcp-authorization-server", "access-policy"]
timestamp: 2026-09-15T18:18:13-07:00
---

# Set up the MCP Authorization Server

Model Context Protocol (MCP), like many other AI-related technologies, is still novel when it comes to security best practices.

This page explains how to configure the Aembit MCP Authorization Server.

## Prerequisites

Before you begin, ensure you have:

* An Aembit Tenant with admin access

* If using user authentication (Enforce SSO on), at least one identity provider configured in **Administration > Identity Providers**:

  * [OIDC 1.0](../../administration/identity-providers/create-idp-oidc.md) - Requires an [OIDC ID Token Trust Provider](../trust-providers/oidc-id-token-trust-provider.md) and a [Credential Provider](../credential-providers/overview.md)
  * [SAML 2.0](../../administration/identity-providers/create-idp-saml.md) - Requires a [SAMLv2 Response Trust Provider](../trust-providers/saml-response-trust-provider.md) and a [Credential Provider](../credential-providers/overview.md)

* An MCP server (cloud, on-premises, or local demo)

* An MCP client (for example, [MCP Jam](https://www.mcpjam.com/) or Gemini CLI)

For details on the differences between OIDC and SAML flows, see [Choosing between OIDC and SAML](../../deploy-install/mcp-authorization-server/overview.md#choosing-between-oidc-and-saml).

## Configure an Access Policy

Configure your Access Policy with these components:

### Create a Client Workload

Create a Client Workload to represent the MCP clients that will request access to your MCP servers. For MCP, use the [**Redirect URI**](../client-workloads/identification/redirect-uri.md) identifier type - this allows MCP clients to register dynamically at runtime through [Dynamic Client Registration](../../deploy-install/mcp-authorization-server/reference-mcp-auth-server.md#dynamic-client-registration-dcr-support). For details on how redirect URIs work in MCP, see [Redirect URIs](../../deploy-install/mcp-authorization-server/concepts-mcp-auth-server.md#redirect-uris).

For general Client Workload configuration guidance, see [Client Workloads](../client-workloads/overview.md).

1. Log into your Aembit Tenant.

2. Go to **Client Workloads** in the left sidebar.

3. Click **+ New** to open the Client Workload form.

4. Enter the **Name** and optional **Description** for your MCP client.

5. Under **Client Identification**, select **Redirect URI** from the dropdown.

6. In the **Value** field, enter the redirect URI that your MCP client uses for OAuth callbacks.

   Each MCP client uses a specific redirect URI for OAuth callbacks. Enter the redirect URI for your client:

   * Local host

     **Local development:**

     | MCP client | Redirect URI                           |
     | ---------- | -------------------------------------- |
     | MCP Jam    | `http://localhost:6274/oauth/callback` |
     | Gemini CLI | `http://localhost:7777/oauth/callback` |

     > **Local development**
     >
     > For local development, you can use either `127.0.0.1` or `localhost`. The MCP specification allows ephemeral ports, so the Authorization Server ignores port numbers in redirect URIs when matching policies against local IP addresses. See [Redirect URIs](../../deploy-install/mcp-authorization-server/concepts-mcp-auth-server.md#redirect-uris).

   * Remote/cloud

     **Remote or cloud-hosted:**

     | MCP client     | Redirect URI                                  |
     | -------------- | --------------------------------------------- |
     | Claude Desktop | `https://claude.ai/api/mcp/auth_callback`     |
     | Custom web app | `https://your-app.example.com/oauth/callback` |

7. (Optional) Under **Custom Claims**, add any claims your MCP server requires from the authenticated user’s identity. Enter each claim as a key-value pair. Values can be strings or arrays.

8. Under **MCP Authorization Configuration**, configure the **Enforce SSO** option:

   * **On** (default) - Users must authenticate through your configured identity provider during the OAuth flow. Configure a [Trust Provider](#create-a-trust-provider) and [Credential Provider](#create-a-credential-provider) in your Access Policy.
   * **Off** - The OAuth flow completes without user authentication. You don’t need a Trust Provider, but you must still configure a [Credential Provider](#create-a-credential-provider).

   For architecture diagrams comparing these flows, see [MCP Authorization Server architecture](../../deploy-install/mcp-authorization-server/concepts-mcp-auth-server.md#mcp-authorization-server-architecture).

9. Click **Save** to create the Client Workload.

### Create a Server Workload

Create a Server Workload to represent the MCP server you want to protect. The configuration must match the URL that MCP clients connect to and your MCP server’s resource server URL. The specific configuration name varies by implementation (for example, FastMCP uses `resource_server_url`).

For general Server Workload configuration guidance, see [Server Workloads](../server-workloads/overview.md).

1. In Aembit, go to **Server Workloads** in the left sidebar.

2. Click **+ New** to open the Server Workload form.

3. Enter the **Name** and optional **Description** for your MCP server.

4. In the **Host** field, enter the hostname where your MCP server runs (for example, `mcp.acme-corp.example.com`).

5. From the **Application Protocol** dropdown, select **MCP**.

6. In the **Port** field, enter the port your MCP server listens on (for example, `443` for HTTPS).

7. (Optional) In the **URL Path** field, enter the path if your MCP server uses one (for example, `/mcp`).

   When you select **MCP** as the application protocol, Aembit automatically configures HTTP Authentication with the Bearer scheme. The **Aembit MCP Authorization Server URL** field displays the auto-generated authorization server URL that MCP clients use for OAuth discovery.

   > **URL alignment**
   >
   > The Host, Port, and URL Path must match exactly with:
   >
   > * The URL your MCP clients connect to
   > * The resource server URL in your MCP server configuration (for example, `resource_server_url` in FastMCP)
   >
   > See [URL configuration alignment](../../deploy-install/mcp-authorization-server/concepts-mcp-auth-server.md#url-configuration-alignment) for details.

8. Click **Save** to create the Server Workload.

### Create a Trust Provider

> **Enforce SSO only**
>
> A Trust Provider is only required when Enforce SSO is on for your Client Workload. If you turned off Enforce SSO, skip this step.

Create a Trust Provider to validate user identity during the MCP authorization flow. The Trust Provider verifies that incoming identity tokens match your expected claims.

For detailed configuration including advanced claim matching, see [OIDC ID Token Trust Provider](../trust-providers/oidc-id-token-trust-provider.md).

1. In Aembit, go to **Trust Providers** in the left sidebar.

2. Click **+ New** to open the Trust Provider form.

3. Enter the **Name** and optional **Description** for your Trust Provider.

4. From the **Trust Provider** dropdown, select **OIDC ID Token**.

5. Configure the **Attestation Method**:

   * **Method** - Select `OIDC Discovery` (recommended for standard OIDC providers).
   * **OIDC Endpoint** - Enter your identity provider’s discovery URL, for example: `https://login.microsoftonline.com/{tenant}/v2.0`.

6. Configure **Match Rules** to validate identity token claims:

   * **Audience (`aud`)** - The intended recipient of the token. Set this to your Aembit identity provider client ID. This ensures your MCP server only accepts tokens issued for your application.
   * **Issuer (`iss`)** - (Optional) The identity provider URL that issued the token.
   * **Subject (`sub`)** - (Optional) The user identifier pattern to match.

   > **Avoid wildcards**
   >
   > You can use `*` as a wildcard to allow any value, but Aembit doesn’t recommend this approach. Wildcards weaken your security posture by allowing tokens from unintended sources. Always specify explicit values when possible, especially for the `aud` (audience) claim.

7. Click **Save**. Aembit displays your new Trust Provider in the list of Trust Providers.

### Configure Access Conditions (optional)

Optionally configure Access Conditions to add additional security requirements such as time-based restrictions or geolocation-based access control. For details, see [Access Conditions](../access-conditions/overview.md).

### Create a Credential Provider

Create a Credential Provider to configure how Aembit issues tokens that MCP servers use to authenticate requests. The Credential Provider type depends on your identity provider protocol:

* **OIDC identity providers:** Use an OIDC ID Token Credential Provider. For configuration details, see [Create an OIDC ID Token Credential Provider](../credential-providers/oidc-id-token.md).
* **SAML identity providers:** Use an OIDC ID Token Credential Provider. Aembit translates the SAML response into an OIDC-compatible token for downstream MCP servers.

1. In Aembit, go to **Credential Providers** in the left sidebar.

2. Click **+ New** to open the Credential Provider form.

3. Enter the **Name** and optional **Description** for your Credential Provider.

4. Under **Credential Type**, select **OIDC ID Token**.

5. Configure the following fields:

   * **Subject** - Select `Dynamic` or `Literal`. Use Dynamic to extract the subject from the incoming identity token. For details, see [Dynamic Claims](../credential-providers/advanced-options/dynamic-claims-oidc.md).
   * **Audience** - Your MCP server’s base URL (for example, `https://mcp.acme-corp.example.com`). Must match the [token audience](../../deploy-install/mcp-authorization-server/concepts-mcp-auth-server.md#token-audience) your server expects.
   * **Lifetime** - Token validity in minutes, for example `60` (1 hour). Adjust based on security requirements.
   * **Signing Algorithm Type** - Select `ES256` or RSA.

   Aembit auto-generates the **Issuer** field based on your tenant configuration.

6. (Optional) Add **Custom Claims** if your MCP server requires additional token claims.

7. Click **Save** to create the Credential Provider.

## Use MCP clients

After configuring your Access Policy, connect an MCP client to your protected MCP server. The following clients support OAuth 2.1 with Dynamic Client Registration, which allows them to automatically discover and authenticate with the Aembit MCP Authorization Server.

Select your MCP client to see configuration instructions:

* MCP Jam

  [MCPJam Inspector](https://www.mcpjam.com/) is an MCP client that provides visual testing and debugging for your MCP servers. It includes an OAuth debugger that displays each step of the authentication flow. This helps troubleshoot issues that are otherwise invisible due to redirects.

  To start the MCPJam Inspector:

  ```shell
  npx @mcpjam/inspector@latest
  ```

  The inspector launches in your browser at `http://127.0.0.1:6274`.

  **Local development configuration:**

  | Field          | Value                        |
  | -------------- | ---------------------------- |
  | **Transport**  | Streamable HTTP              |
  | **Server URL** | `http://localhost:8000/mcp`  |
  | **Auth**       | OAuth 2.1 with Dynamic (DCR) |

  **Remote server configuration:**

  | Field          | Value                                 |
  | -------------- | ------------------------------------- |
  | **Transport**  | Streamable HTTP                       |
  | **Server URL** | `https://your-server.example.com/mcp` |
  | **Auth**       | OAuth 2.1 with Dynamic (DCR)          |

  When running the inspector in Docker and connecting to a host machine server, use `http://host.docker.internal:PORT` instead of `http://localhost:PORT`.

  The debugger displays each step of the authorization flow:

  1. Initial MCP request (401 response)
  2. Metadata retrieval
  3. Dynamic client registration
  4. Authorization request
  5. Token exchange
  6. Authenticated MCP request

  MCPJam Inspector supports Standard Input/Output (STDIO), Server-Sent Events (SSE), and streamable HTTP connections, with OAuth 2.1 and bearer token authentication.

  **Network considerations:** MCPJam uses a backend proxy server to fetch OAuth metadata. If your MCP server has restricted network access, you may need to allow MCPJam’s proxy IP ranges. See [MCPJam backend proxy error](../../deploy-install/mcp-authorization-server/troubleshooting-mcp-auth-server.md#mcpjam-backend-proxy-error) for details.

* Gemini CLI

  Gemini CLI supports automatic discovery and registration with the MCP Authorization Server.

  **Key details:**

  * Supports OAuth 2.0 authentication for remote MCP servers
  * Automatic OAuth discovery for servers that support it
  * Manages tokens automatically after initial authentication

  **Authentication commands:**

  * `/mcp auth` - List servers requiring authentication
  * `/mcp auth serverName` - Authenticate with a specific server

  Add your MCP server to the Gemini CLI [settings.json](https://geminicli.com/docs/tools/mcp-server/) file:

  | Scope         | File path                 |
  | ------------- | ------------------------- |
  | User (global) | `~/.gemini/settings.json` |
  | Project       | `.gemini/settings.json`   |

  Add the `mcpServers` configuration:

  ```json
  {
    "mcpServers": {
      "TestMCPServer": {
        "httpUrl": "http://localhost:8000/mcp"
      }
    }
  }
  ```

  The CLI handles OAuth discovery and registration automatically when it encounters a 401 response from the MCP server.

* Claude Desktop

  Claude Desktop supports MCP servers through **Settings > Connectors**.

  > **Adding remote MCP servers**
  >
  > Add remote MCP servers through the Connectors UI, not via `claude_desktop_config.json`.

  **Key details:**

  * Supports OAuth 2.1 with Dynamic Client Registration (DCR)
  * OAuth callback URL: `https://claude.ai/api/mcp/auth_callback`
  * Available on Pro, Max, Team, and Enterprise plans

  **Configuration:**

  1. Navigate to **Settings > Connectors**
  2. Add your MCP server URL (for example, `http://localhost:8000/mcp`)
  3. Optionally configure OAuth `client_id` and `client_secret` in **Advanced settings**
  4. Complete OAuth authentication when prompted

  For more information, see [Building Custom Connectors via Remote MCP Servers](https://support.claude.com/en/articles/11503834-building-custom-connectors-via-remote-mcp-servers).

* Claude Code

  Claude Code supports MCP servers through the CLI or configuration files.

  **Key details:**

  * Supports OAuth 2.0 for MCP servers
  * Supports Dynamic Client Registration (DCR)
  * Uses `/mcp` command to manage authentication
  * Automatic token storage and refresh

  **Add via CLI:**

  ```shell
  claude mcp add --transport http test-mcp-server http://localhost:8000/mcp
  ```

  **Add via `.mcp.json`:**

  ```json
  {
    "mcpServers": {
      "TestMCPServer": {
        "url": "http://localhost:8000/mcp"
      }
    }
  }
  ```

  | Scope                 | File path                   |
  | --------------------- | --------------------------- |
  | Project (team-shared) | `.mcp.json` at project root |
  | User                  | `claude mcp add` command    |

  To authenticate, run `/mcp` within Claude Code and select **Authenticate**.

  For more information, see [Claude Code MCP Documentation](https://code.claude.com/docs/en/mcp).

If you encounter authentication errors, see [Troubleshoot the MCP Authorization Server](../../deploy-install/mcp-authorization-server/troubleshooting-mcp-auth-server.md).

## MCP server requirements

To work with Aembit’s MCP Authorization Server, your MCP server needs certain configuration settings. Most of these are standard OAuth concepts from the MCP specification—the exact field names vary by MCP server implementation.

| Concept                      | Purpose                                           | Aembit value                                                                    | Required by |
| ---------------------------- | ------------------------------------------------- | ------------------------------------------------------------------------------- | ----------- |
| **Authorization Server URL** | Where MCP clients discover OAuth endpoints        | `https://[tenant].mcp.[region].aembit.io`                                       | MCP spec    |
| **Token Issuer**             | OIDC issuer URL of token validation               | `https://[tenant].id.[region].aembit.io`                                        | RFC 8414    |
| **JWKS URI**                 | Public keys used for token signature verification | `https://[tenant].mcp.[region].aembit.io/.well-known/openid-configuration/jwks` | RFC 8414    |
| **Token Audience**           | Must match your Credential Provider configuration | Your MCP server’s public URL                                                    | RFC 8707    |
| **Token Algorithm**          | Signing algorithm for access tokens               | `ES256` (default) or RSA                                                        | Aembit      |

> **Token algorithm**
>
> The MCP specification doesn’t mandate a specific signing algorithm. Aembit supports ES256 (default) and RSA—configure your MCP server’s token verifier to match your Credential Provider settings.

Consult your MCP server’s documentation for how to configure these settings. For a complete reference, see [Configuration concepts](../../deploy-install/mcp-authorization-server/reference-mcp-auth-server.md#configuration-concepts).

> **Issuer subdomain**
>
> When configuring your MCP server’s token verification, the `issuer` must use the `.id.` subdomain (for example, `abc123.id.useast2.aembit.io`), **not** the `.mcp.` subdomain. See [Tenant URL patterns](../../deploy-install/mcp-authorization-server/reference-mcp-auth-server.md#tenant-url-patterns) for details.

## Test with a demo MCP server

If you don’t have an existing MCP server, you can use this [FastMCP](https://gofastmcp.com/) demo server to test your Aembit configuration. This example shows one way to configure the settings from the preceding table—your production MCP server may use different field names or configuration methods.

Replace the placeholder values with your Aembit tenant details, then run with `python server.py`.

* server.py

  ```python
  from fastmcp import FastMCP
  from fastmcp.server.auth import RemoteAuthProvider
  from fastmcp.server.auth.providers.jwt import JWTVerifier
  from pydantic import AnyHttpUrl
  import json


  # Replace [your-tenant-id] and [region] with your Aembit tenant details.
  # Find these values in the Server Workload form after selecting MCP protocol.
  cfg = {
      "host": "0.0.0.0",
      "port": 8000,
      "mcp_server_url": "http://localhost:8000",
      # Authorization server uses .mcp. subdomain
      "auth_server": "https://[your-tenant-id].mcp.[region].aembit.io",
      # Token issuer uses .id. subdomain (NOT .mcp.)
      "issuer": "https://[your-tenant-id].id.[region].aembit.io",
      "jwks_uri": "https://[your-tenant-id].mcp.[region].aembit.io/.well-known/openid-configuration/jwks",
  }


  # Configure JWT verification against Aembit's JWKS endpoint
  token_verifier = JWTVerifier(
      jwks_uri=cfg["jwks_uri"],
      issuer=cfg["issuer"],
      audience=cfg["mcp_server_url"],  # Audience must match server URL
      algorithm="ES256",  # Or "RS256" if using RSA in your Credential Provider
  )


  # Configure OAuth 2.1 discovery - returns 401 with auth server URL
  auth = RemoteAuthProvider(
      token_verifier=token_verifier,
      authorization_servers=[AnyHttpUrl(cfg["auth_server"])],
      base_url=cfg["mcp_server_url"],
  )


  # Initialize server with authentication
  mcp = FastMCP(
      "Aembit Test MCP Server",
      host=cfg["host"],
      port=cfg["port"],
      auth=auth,
  )




  @mcp.tool()
  def get_server_status() -> str:
      """Get server status - confirms authentication succeeded."""
      return json.dumps({
          "server": "Aembit Test MCP Server",
          "status": "running",
          "authenticated": True,
          "message": "Successfully authenticated via Aembit!"
      })




  if __name__ == "__main__":
      print(f"Starting server on {cfg['mcp_server_url']}/mcp")
      mcp.run(transport="streamable-http")
  ```

* requirements.txt

  ```text
  fastmcp>=2.11.0
  httpx
  uvicorn
  pyjwt[crypto]
  pydantic
  ```

**Key configuration notes:**

* The `issuer` uses the `.id.` subdomain (for example, `abc123.id.useast2.aembit.io`), not `.mcp.`
* The `algorithm` must match your Credential Provider setting—ES256 (default) or RSA (see [Token algorithm](../../deploy-install/mcp-authorization-server/reference-mcp-auth-server.md#token-algorithm))
* The `audience` must match your server’s public URL exactly

For URL configuration details, see [URL configuration alignment](../../deploy-install/mcp-authorization-server/concepts-mcp-auth-server.md#url-configuration-alignment).

## Troubleshooting

For common errors and solutions, see [Troubleshoot the MCP Authorization Server](../../deploy-install/mcp-authorization-server/troubleshooting-mcp-auth-server.md).

## Next steps

* Review the [MCP Authorization Server reference](../../deploy-install/mcp-authorization-server/reference-mcp-auth-server.md) for configuration options and endpoints
* Learn more about [Access Policies](../overview.md)
