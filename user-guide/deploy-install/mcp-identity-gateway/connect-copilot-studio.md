---
type: how-to
title: "Connect Microsoft Copilot Studio"
description: "Connect Microsoft Copilot Studio agents to enterprise MCP servers through the Aembit MCP Identity Gateway."
resource: https://docs.aembit.io/user-guide/deploy-install/mcp-identity-gateway/connect-copilot-studio/
interface: mcp
tags: ["mcp-identity-gateway", "deploy-install"]
timestamp: 2026-09-15T18:18:13-07:00
---

# Connect Microsoft Copilot Studio

Microsoft Copilot Studio agents can reach your enterprise MCP servers through the Aembit Model Context Protocol (MCP) Identity Gateway. Copilot Studio connects to the Gateway as an MCP client, and the Gateway enforces your Aembit Access Policies and presents per-user credentials to each downstream MCP server.

This guide covers the Copilot Studio side of the connection. For the Aembit Tenant configuration, see [Set up the MCP Identity Gateway](../../access-policies/mcp-identity-gateway/setup-mcp-gateway.md).

## How it works

The following diagram shows the high-level flow from a Copilot Studio agent through the Gateway to an MCP server:

![Copilot Studio agent connecting through the Aembit MCP Identity Gateway to an MCP server](https://docs.aembit.io/d2/docs/user-guide/deploy-install/mcp-identity-gateway/connect-copilot-studio-0.svg)

1. A Copilot Studio agent connects to the Gateway over Streamable HTTP transport and presents an OAuth 2.0 access token that identifies the agent and user.
2. The Gateway validates the token, requests policy evaluation from Aembit Cloud, and proxies the request to the target MCP server using credentials that Aembit manages.
3. The MCP server processes the request and returns a response.
4. The Gateway relays the response back to the Copilot Studio agent.

Copilot Studio never receives credentials for the MCP server. The Gateway handles credential exchange on each request.

## Prerequisites

Before you begin, ensure you have:

* A provisioned MCP Identity Gateway endpoint running version `1.32.5006` or higher.
* Both Access Policies from [Set up the MCP Identity Gateway](../../access-policies/mcp-identity-gateway/setup-mcp-gateway.md) configured and active: client-to-Gateway and Gateway-to-server.
* A Microsoft Copilot Studio tenant with licensing that allows custom agents and tools.
* Permission in Copilot Studio to create agents, add tools, and create connections.
* An Aembit role that can edit the Client Workload for the client-to-Gateway policy, so you can add the Copilot Studio redirect URI.

## Create a Copilot Studio agent

If you don’t already have an agent to connect, create one.

1. In Copilot Studio, go to **Agents** and click **+ Create a Blank Agent**.
2. Enter a name for the agent.
3. Click **Create**.

## Create the MCP tool in Copilot Studio

Create a tool that points Copilot Studio at your Gateway endpoint.

1. In Copilot Studio, go to **Tools** and click **+ New Tool**.

2. Select **Model Context Protocol**.

3. Configure the tool with the following settings:

   | Field              | Value                                                                                         |
   | ------------------ | --------------------------------------------------------------------------------------------- |
   | Friendly name      | A short, descriptive name for the tool                                                        |
   | Server description | A required description of the MCP server                                                      |
   | MCP Gateway URL    | Your Gateway endpoint with the `/mcp` path suffix (for example, `https://<gateway-host>/mcp`) |
   | Authentication     | Select **OAuth 2.0**                                                                          |
   | Type               | Select **Dynamic Discovery**                                                                  |

4. Click **Create**. After Copilot Studio creates the tool, refresh your browser to see it.

> **The Gateway URL must end in `/mcp`**
>
> The Gateway exposes MCP traffic at the `/mcp` path. Use `https://<gateway-host>/mcp` instead of the deprecated `/me` path.

> **Recreate the tool after a Gateway upgrade**
>
> Creating the tool caches the OAuth-protected-resource parameters but doesn’t establish a connection. Delete and recreate the tool after you upgrade the Gateway to a Copilot-compatible version. Copilot Studio then re-reads the OAuth-protected-resource metadata.

## Assign the tool to the agent and connect

Assign the tool to your agent and complete the OAuth connection. The first connection attempt surfaces the Copilot Studio redirect URI in Aembit, which you then add to the Client Workload.

1. In Copilot Studio, go to **Agents**, open your agent, and select the **Tools** tab.

2. Click **+ Add a tool**, select **All**, and search for your tool’s friendly name.

3. Select the tool, open the **Not Connected** dropdown, and click **Create new connection**, then **Create**. An OAuth pop-up opens and Copilot Studio attempts to authenticate.

4. Capture the redirect URI and add it to the Client Workload:

   1. In your Aembit Tenant, open MCP Authorization Tracing to view the inbound authorization request and copy the full redirect URI.
   2. Add the redirect URI to the **Client Workload** in your client-to-Gateway Access Policy.

   For testing, the wildcard redirect URI `https://global.consent.azure-apim.net/redirect/*` is acceptable. For production, add the exact redirect URI captured from MCP Authorization Tracing.

5. Retry the connection. Complete single sign-on (SSO) and the OAuth consent flow. When the connection succeeds, the dropdown turns green.

6. Click **+ Add and Configure**. This step fetches the OAuth access token.

> **Sign-in fails without the redirect URI**
>
> If the Client Workload doesn’t yet include the redirect URI, the connection fails during sign-in. Add the redirect URI from MCP Authorization Tracing to the Client Workload, then retry the connection.

## Test the connection

1. Open your agent and click **Test**.
2. Ask the agent: **“What MCP tools do you have access to?”** Copilot Studio prompts you to connect first.
3. Click **Connect**, then **Submit** to establish the MCP connection.
4. Return to the **Test** panel and click **Retry**. The agent lists the tools available through the Gateway.

## Connect the Microsoft MCP Server for Enterprise (Microsoft Graph)

The Microsoft MCP Server for Enterprise, which exposes Microsoft Graph, doesn’t support OAuth discovery. Configure its Credential Provider manually in the Gateway-to-server Access Policy. Use the **MCP User-Based Access Token** Credential Provider type.

> **Use user-based authentication**
>
> The Microsoft MCP Server for Enterprise requires **user-based** authentication.

Before configuring the Credential Provider, register an application in Microsoft Entra ID:

1. Create the Aembit Credential Provider first (without the OAuth client values), then copy its read-only **Callback URL**.
2. In Microsoft Entra ID, create a single-tenant app registration with the **Web** platform and set the redirect URI to the **Callback URL** you copied. The value takes the form `https://<tenantId>.id.<region>.aembit.io/mcp-auth/userauth/<cp-id>/callback`.
3. Copy the **Application (client) ID** for use as the Client ID.
4. Create a client secret and copy its **Value** for use as the Client Secret.
5. Add the **Microsoft MCP Server for Enterprise** API permission. Granting the permission requires the **Application Administrator** or **Cloud Application Administrator** role.
6. Note your Microsoft Entra **tenant ID** (a GUID) for the OAuth URLs.

> **Existing Credential Providers keep their original callback URL**
>
> Aembit changed the callback URL format for MCP User-Based Access Token Credential Providers. Providers created before that change keep their original callback URL, which takes the form `https://<tenantId>.mcp.<region>.aembit.io/userauth/<cp-id>/callback`.
>
> Updating a Credential Provider doesn’t regenerate its callback URL, so you may see either format depending on when you created the provider. Both formats remain valid, and you don’t need to take any action for existing providers.
>
> Always copy the exact value shown in the Credential Provider rather than constructing the URL yourself, and register that value with the MCP server’s own OAuth authorization server.

Then configure the **MCP User-Based Access Token** Credential Provider in the Gateway-to-server Access Policy. For the full configuration reference, see [Configure MCP User-Based Access Token](../../access-policies/credential-providers/mcp-user-based-access-token.md). Because discovery isn’t available for this server, enter the OAuth values manually:

| Field             | Value                                                                       |
| ----------------- | --------------------------------------------------------------------------- |
| MCP Server URL    | `mcp.svc.cloud.microsoft/enterprise`                                        |
| Authorization URL | `https://login.microsoftonline.com/<azure-tenant-id>/oauth2/v2.0/authorize` |
| Token URL         | `https://login.microsoftonline.com/<azure-tenant-id>/oauth2/v2.0/token`     |
| Scopes            | `api://e8c77dc2-69b3-43f4-bc51-3213c9d915b4/.default`                       |
| Resource          | `e8c77dc2-69b3-43f4-bc51-3213c9d915b4`                                      |
| Client ID         | The Application (client) ID from your app registration                      |
| Client Secret     | The client secret value from your app registration                          |
| PKCE Required     | Enabled                                                                     |

> **Add `offline_access` to Scopes**
>
> You can optionally append `offline_access` to the **Scopes** value, so it reads `api://e8c77dc2-69b3-43f4-bc51-3213c9d915b4/.default offline_access`. Microsoft Entra ID then issues a refresh token for unattended token renewal.

For the matching Server Workload, use these values:

| Field                | Value                     |
| -------------------- | ------------------------- |
| Host                 | `mcp.svc.cloud.microsoft` |
| Port                 | `443` (TLS enabled)       |
| URL Path             | `/enterprise`             |
| Application Protocol | MCP                       |

## Limitations and troubleshooting

Copilot Studio has some limitations. The following guidance helps you work with them.

### Practical tool limit

> **Microsoft Copilot Studio limitation**
>
> While the Aembit MCP Identity Gateway places no limit on tools, Microsoft Copilot Studio limits how many MCP tools an agent can load.

In practice, Copilot Studio drops tools once the total across your assigned MCP servers exceeds 70, showing an error message instead of loading them all.

Because the Gateway returns tools in the order the upstream servers respond, the specific tools that drop vary between sessions.

To stay within the limit:

* Assign fewer Access Policies to each agent.
* Use Copilot Studio’s per-tool toggles to enable only the tools the agent needs.
* Split integrations across child agents, where each child agent has its own tool budget.

### Entity and tool name length

* Keep **Server Workload** and **Credential Provider** names to 15 characters or fewer. Longer names can cause silent connection failures.
* Keep MCP tool names to 64 characters or fewer, including the server-name prefix that the Gateway adds.

### Copilot Studio caches the tool list

Copilot Studio caches the list of tools it loads from the Gateway. After a Gateway upgrade or a policy change, refresh your browser or delete and recreate the tool so Copilot Studio re-reads the current tool list.

### Sign-in fails behind a web application firewall

This applies only if you self-host the Gateway; the managed MCP Identity Gateway isn’t affected.

A Web Application Firewall (WAF) or other HTTP protection device in front of the Gateway can block requests it identifies as suspicious. Sign-in then fails before the request reaches the Gateway, and your Aembit Tenant shows no Access Authorization or Workload events.

See [Allow MCP client traffic through your WAF](self-host-mcp-gateway.md#allow-mcp-client-traffic-through-your-waf) for what to check.

## Next steps

* [Set up the MCP Identity Gateway](../../access-policies/mcp-identity-gateway/setup-mcp-gateway.md) - Configure the two Access Policies the Gateway requires
* [MCP Identity Gateway concepts](concepts-mcp-gateway.md) - Architecture, security model, and token handling
* [MCP Authorization Tracing](../../audit-report/mcp-authorization-tracing.md) - View inbound MCP authorization requests at the Gateway
