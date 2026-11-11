---
type: how-to
title: "Set up embedded user authorization"
description: "Configure your Aembit Tenant so your application can authorize people for MCP servers."
resource: https://docs.aembit.io/user-guide/access-policies/mcp-identity-gateway/setup-embedded-user-authorization/
interface: mcp
tags: ["mcp-identity-gateway", "access-policy"]
timestamp: 2026-11-10T23:08:08-08:00
---

# Set up embedded user authorization

Configure embedded user authorization in your Aembit Tenant when your application has its own UI, such as a portal or AI agent platform, so people can connect MCP servers without leaving it.

Copy an Aembit Client ID from an OIDC Identity Provider, create a Client-to-Gateway Policy for your application, and create a Gateway-to-Server Policy for each MCP server. For a full explanation of the flow, see [Embedded user authorization](embedded-user-authorization.md).

## Prerequisites

* An MCP Identity Gateway endpoint for your Aembit Tenant. See [Request your MCP Identity Gateway endpoint](setup-mcp-gateway.md#request-your-mcp-identity-gateway-endpoint).
* An active OIDC 1.0 Identity Provider in your Aembit Tenant, whose ID token includes the person’s email address. See [Create an OIDC 1.0 Identity Provider](../../administration/identity-providers/create-idp-oidc.md).
* An OAuth public client in your application that uses the authorization code flow with PKCE and can add a custom parameter to the authorize request.

## Copy the Aembit Client ID

Copy the **Aembit Client ID** from your OIDC 1.0 Identity Provider in your Aembit Tenant’s administration settings. Your application sends it as `client_id` to identify itself to Aembit.

1. Log into your Aembit Tenant.

2. Click **Administration** in the left sidebar.

3. Select **Administration ☰ Identity Providers**.

4. Click your OIDC 1.0 Identity Provider to open it.

5. On the **Details** tab, copy the **Aembit Client ID**.

   Aembit shows the **Aembit Client ID** only on a saved OIDC 1.0 Identity Provider, after **Aembit Redirect URL**.

## Create the client-to-gateway Access Policy

Create the Client-to-Gateway Policy in the Access Policy builder, one card at a time, in the order of the following sections.

1. In your Aembit Tenant, go to **Access Policies** and click **+ New**.
2. In the **Name** field, enter a name for the Access Policy.

### Client Workload

Create a new Client Workload that represents only your application. The **Enforce SSO** settings apply to every MCP client the Client Workload matches, so do not reuse a Client Workload that other MCP clients match.

1. In the **Client Workload** card, click **+ Configure**, and select the **Add New** tab.

2. Under **Client Identification**, select **Redirect URI**, and enter your application’s callback URL as the **Value**.

   A value that ends in `/*` matches any path under it on the same scheme, host, and port. See [Redirect URI](../client-workloads/identification/redirect-uri.md).

3. Under **MCP Authorization Configuration**, leave **Enforce SSO** on.

   With **Enforce SSO** off, the person does not sign in or consent at the MCP server, so your application gets no access to the MCP server for that person.

4. In the Identity Provider list under **Enforce SSO**, keep only the Identity Provider whose Aembit Client ID your application uses.

   With more than one active Identity Provider in the list, Aembit stops the flow and returns the person’s browser to your application with an error.

5. Click **Save**.

### Server Workload

1. In the **Server Workload** card, click **+ Configure**.
2. On the **Select Existing** tab, select the Server Workload that represents MCP Identity Gateway. If none exists, create one on the **Add New** tab as [Server Workload](setup-mcp-gateway.md#server-workload) describes.
3. Note the Server Workload’s URL, such as `https://<tenant-id>.mcpgateway.aembit.io/mcp`. Your application sends this URL as `resource`.

When the `resource` your application sends does not match this Server Workload, Aembit stops the flow and returns the person’s browser to your application with an error.

### Trust Provider

In the **Trust Provider** card, select an existing Trust Provider on the **Select Existing** tab, or create one as [Trust Provider](setup-mcp-gateway.md#trust-provider) describes.

### Credential Provider

1. In the **Credential Provider** card, click **+ Configure**, and select the **Add New** tab.
2. Set **Credential Type** to **OIDC ID Token** and **Audience** to the MCP Identity Gateway URL. Set the other fields as [Credential Provider](setup-mcp-gateway.md#credential-provider) describes.
3. To let your application refresh its access token, turn on **Enable Refresh Token Support** and set **Absolute Token Lifetime**.
4. Click **Save**.

The access token your application gets at the end of the flow comes from this Credential Provider and lasts for its lifetime.

> **Refresh token support sets how often people go back to the MCP server**
>
> With **Enable Refresh Token Support** turned off, your application gets no refresh token. Each time the access token ends, your application must run the flow again. With an MCP User-Based Access Token on the Gateway-to-Server Policy, Aembit then sends the person back to the MCP server.
>
> To let your application refresh the access token instead, turn on **Enable Refresh Token Support** and set **Absolute Token Lifetime**. Your application runs the flow again only after the **Absolute Token Lifetime** ends. See [Consent at the MCP server](reference-embedded-user-authorization.md#consent-at-the-mcp-server).

With an Aembit Access Token Credential Provider, the flow also completes. MCP Identity Gateway then rejects the access token, because its audience is not the MCP Identity Gateway URL. With an MCP User-Based Access Token Credential Provider, the token request fails with `invalid_request`.

### Save the Access Policy

1. In the **Access Policy** status card, verify that each required component shows a green **Configured** status.
2. In the header, click **Save Policy & Activate**.

## Create a gateway-to-server Access Policy for each MCP server

Each MCP server your application authorizes people for needs a Gateway-to-Server Policy that meets the following requirements. Use an existing Gateway-to-Server Policy that meets them, or create one by following [Create the gateway-to-server Access Policy](setup-mcp-gateway.md#create-the-gateway-to-server-access-policy).

* The Server Workload must point to the same MCP server URL your application requests. For how Aembit compares the two, see [MCP server URL rules](reference-embedded-user-authorization.md#mcp_server_url-rules).
* Only one Gateway-to-Server Policy available to the person can match each MCP server. When more than one matches, Aembit stops the flow and returns the person’s browser to your application with an error.
* The Access Policy must have exactly one active MCP User-Based Access Token or SSO Access Token Passthrough Credential Provider, and SSO Access Token Passthrough skips consent at the MCP server. MCP Enterprise Managed Access Token and OAuth 2.0 Authorization Code do not work with embedded user authorization.

## Values your application sends

Your application uses these values to build the authorize and token requests. Replace `<tenant-id>` with your Aembit Tenant ID and `<stack>` with your Aembit Tenant’s stack.

| Value                    | Where to find it                                             | Request parameter |
| ------------------------ | ------------------------------------------------------------ | ----------------- |
| Aembit Client ID         | The OIDC Identity Provider’s **Details** tab                 | `client_id`       |
| Redirect URI             | The Client Workload’s **Redirect URI** identifier            | `redirect_uri`    |
| MCP Identity Gateway URL | The Server Workload in the Client-to-Gateway Policy          | `resource`        |
| MCP server URL           | The Server Workload in each Gateway-to-Server Policy         | `mcp_server_url`  |
| Authorize endpoint       | `https://<tenant-id>.id.<stack>.aembit.io/connect/authorize` | None              |
| Token endpoint           | `https://<tenant-id>.id.<stack>.aembit.io/connect/token`     | None              |

## Next steps

* [Embedded user authorization reference](reference-embedded-user-authorization.md) - Request parameters, lifetimes, errors, and events
* [Embedded user authorization](embedded-user-authorization.md) - How the flow works
