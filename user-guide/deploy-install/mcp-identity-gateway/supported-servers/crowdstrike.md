---
type: how-to
title: "CrowdStrike Falcon MCP Server"
description: "Configure the CrowdStrike Falcon MCP Server to work with AI agents through the Aembit MCP Identity Gateway."
resource: https://docs.aembit.io/user-guide/deploy-install/mcp-identity-gateway/supported-servers/crowdstrike/
interface: mcp
tags: ["supported-server", "mcp-identity-gateway", "deploy-install"]
timestamp: 2026-11-09T14:11:42-08:00
---

# CrowdStrike Falcon MCP Server

Aembit supports the open source [CrowdStrike Falcon MCP server](https://github.com/CrowdStrike/falcon-mcp), which lets AI agents query CrowdStrike Falcon detections, hosts, intelligence, and other security data through MCP tools.

You run the Falcon MCP server yourself as an MCP server. The Aembit MCP Identity Gateway authenticates to it with a static API key that all users share.

This guide builds the Gateway-to-Server Policy—the second of the two Access Policies the MCP Identity Gateway requires. You create the first, the Client-to-Gateway Policy, during [Gateway setup](../../../access-policies/mcp-identity-gateway/setup-mcp-gateway.md).

## Prerequisites

Before you begin, ensure you have the following:

* A paid CrowdStrike Falcon subscription with API access, and permission to create API clients in the Falcon console
* A host where you can run `falcon-mcp` behind a TLS reverse proxy that MCP Identity Gateway can reach
* A configured [Aembit MCP Identity Gateway](../../../access-policies/mcp-identity-gateway/setup-mcp-gateway.md)

## Requirements and considerations

Before you configure CrowdStrike Falcon, review these requirements and behaviors specific to the Falcon MCP server.

* **Use an API Key Credential Provider.** The Falcon MCP server accepts one static API key and has no OAuth or per-user authentication. Every user who reaches it through MCP Identity Gateway shares that key and the Falcon API client’s permissions.
* **You host the server.** You run CrowdStrike’s [`falcon-mcp`](https://github.com/CrowdStrike/falcon-mcp) server in your own infrastructure, and Aembit-managed MCP Identity Gateway deployments connect only to MCP servers at publicly reachable addresses.
* **Aembit provides only the server’s API key.** The server keeps the Falcon API client ID and secret in its own configuration and uses them to call the Falcon API.
* **The default tool set exceeds the Copilot Studio tool limit.** With no module selection, `falcon-mcp` enables every module and registers more than 100 tools, while [Copilot Studio drops tools past about 70](../connect-copilot-studio.md#practical-tool-limit).
* **The default tool set can change your Falcon environment.** Many modules carry tools that create, update, or delete Falcon resources, and `FALCON_MCP_READ_ONLY=true` registers only read-only tools.
* **The server is pre-1.0.** CrowdStrike can change tool names, parameters, and responses between minor releases, so pin a version.

## Run the Falcon MCP server

CrowdStrike does not host an MCP endpoint, so you run `falcon-mcp` yourself and put a TLS endpoint in front of it. CrowdStrike’s [falcon-mcp repository](https://github.com/CrowdStrike/falcon-mcp) covers installation and every configuration option.

1. In the Falcon console, go to **Support and resources > API clients and keys** and create an API client with the scopes for the modules you plan to enable. Note the client ID, the client secret, and your Falcon region’s API base URL.

2. Generate a random API key for the MCP server, for example with `openssl rand -hex 32`. The Aembit Credential Provider stores this key later.

3. Configure `falcon-mcp` with the following environment variables:

   | Variable               | Value                                               |
   | ---------------------- | --------------------------------------------------- |
   | `FALCON_CLIENT_ID`     | The API client ID                                   |
   | `FALCON_CLIENT_SECRET` | The API client secret                               |
   | `FALCON_BASE_URL`      | Your Falcon region’s API base URL                   |
   | `FALCON_MCP_TRANSPORT` | `streamable-http`                                   |
   | `FALCON_MCP_API_KEY`   | The API key you generated                           |
   | `FALCON_MCP_READ_ONLY` | `true`, unless AI agents need to change Falcon data |

4. Start `falcon-mcp`. It serves the MCP endpoint at `/mcp` over plain HTTP, on `127.0.0.1:8000` by default.

5. Put a TLS reverse proxy in front of the server, such as nginx with a certificate from a public certificate authority. Keep the plain HTTP port off the network, because MCP Identity Gateway sends the API key in every request. Note the reverse proxy’s host and port, such as `https://<falcon-mcp-host>:9000/mcp`.

## Configure the Credential Provider

Create an API Key Credential Provider in Aembit.

1. Log into your Aembit Tenant.

2. Go to **Credential Providers** in the left sidebar and click **+ New**.

3. Configure the following fields:

   | Field               | Value                                                    |
   | ------------------- | -------------------------------------------------------- |
   | **Name**            | A user-friendly name                                     |
   | **Credential Type** | API Key                                                  |
   | **API Key**         | The value you set for `FALCON_MCP_API_KEY` on the server |

4. Click **Save**.

## Create the Server Workload

1. Go to **Server Workloads** in the left sidebar and click **+ New**.

2. Configure the following fields:

   | Field                     | Value                                           |
   | ------------------------- | ----------------------------------------------- |
   | **Name**                  | A user-friendly name                            |
   | **Host**                  | The domain name of your TLS reverse proxy       |
   | **Application Protocol**  | MCP                                             |
   | **Port**                  | The reverse proxy’s port, with **TLS** selected |
   | **URL Path**              | `/mcp`                                          |
   | **Authentication Method** | API Key                                         |
   | **Authentication Scheme** | Header                                          |
   | **Header**                | `x-api-key`                                     |

3. Click **Save**.

## Create an Access Policy

On the **Select Existing** tab, select the API Key Credential Provider you created and click **Use Selected**. The linked setup procedure describes creating a new Credential Provider.

This section creates the Gateway-to-Server Access Policy, which authorizes MCP Identity Gateway to access CrowdStrike Falcon.

Create an Access Policy linking MCP Identity Gateway (as the Client Workload), the Credential Provider you created, and the Server Workload for CrowdStrike Falcon.

> **The Gateway is the Client Workload**
>
> In this policy, the Client Workload is MCP Identity Gateway itself—not the AI agent or individual users. The AI agent connects through the separate Client-to-Gateway policy. The Credential Provider injects the same credential for every user.

For step-by-step instructions, including the Client Workload settings that identify MCP Identity Gateway, see [Create the gateway-to-server Access Policy](../../../access-policies/mcp-identity-gateway/setup-mcp-gateway.md#create-the-gateway-to-server-access-policy).

## Verify

After you create the Access Policy, connect an AI agent through MCP Identity Gateway and list its tools. The list includes the `falcon_`-prefixed tools that your `falcon-mcp` configuration enables.
