---
type: how-to
title: "CIMD Client ID"
description: "How to identify MCP client workloads using a Client ID Metadata Document (CIMD) URL in Aembit"
resource: https://docs.aembit.io/user-guide/access-policies/client-workloads/identification/cimd-client-id/
interface: web-ui
tags: ["identification", "client-workload", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# CIMD Client ID

A Client ID Metadata Document (CIMD) is a JSON file that an MCP client hosts at a public HTTPS URL. The document describes the client’s name and its allowed redirect URIs. The URL itself serves as the client’s identity. When the client presents the URL-formatted `client_id`, Aembit fetches the document from that URL and validates the client’s metadata.

Aembit fetches and processes a metadata document only when an administrator has explicitly added its URL to a Client Workload’s identity configuration. This ensures only MCP clients you register can obtain access tokens through your MCP Authorization Server.

For more about how MCP authorization flows work, see [MCP Authorization Server concepts](../../../../ai-guide/mcp/authorization-server/concepts-mcp-auth-server.md).

## Applicable deployment type

This method is for [MCP Authorization Server](../../../../ai-guide/mcp/authorization-server/overview.md) deployments. It identifies MCP clients that connect to MCP servers protected by Aembit’s MCP Authorization Server.

> **Authentication scope**
>
> For CIMD Client Workloads, Aembit identifies the client by its metadata document URL. Aembit doesn’t perform JWKS validation or `private_key_jwt` token authentication.

## CIMD document requirements

Aembit fetches the metadata document when you save the Client Workload and again during each authorization flow. The document and its URL must meet the following requirements, or Aembit rejects the fetch:

| Requirement       | Detail                                                                                                                                  |
| ----------------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| Scheme            | The URL must use `https`. Aembit rejects scheme-relative and non-HTTPS URLs.                                                            |
| URL form          | The URL must not contain query strings, fragments, or user information.                                                                 |
| Network access    | Aembit must reach the URL directly. Aembit blocks redirects and private or reserved IP ranges, such as loopback and RFC 1918 addresses. |
| Content-Type      | The response must be `application/json`.                                                                                                |
| Maximum size      | The document must not exceed 8 KB.                                                                                                      |
| `client_id` match | The `client_id` field inside the document must exactly match the URL that Aembit fetches it from. A mismatch aborts the flow.           |

The following example shows a CIMD metadata document:

**client-metadata.json**

```json
{
  "client_id": "https://mcp.example.com/.well-known/oauth/client-metadata.json",
  "client_name": "Example MCP Client",
  "client_uri": "https://mcp.example.com",
  "logo_uri": "https://mcp.example.com/logo.png",
  "redirect_uris": [
    "https://app.example.com/oauth/callback",
    "http://127.0.0.1:6274/oauth/callback",
    "http://localhost:6274/oauth/callback"
  ],
  "grant_types": ["authorization_code", "refresh_token"],
  "response_types": ["code"],
  "token_endpoint_auth_method": "none",
  "application_type": "native"
}
```

## Create a Client Workload with a CIMD Client ID identifier

To configure a Client Workload using the CIMD Client ID identifier, follow these steps:

1. Log into your Aembit Tenant.

2. Click **Client Workloads**.

3. Click **+ New** to open the Client Workload editor panel.

4. Enter the **Name** and optional **Description** for the Client Workload.

5. Under **Client Identification**, select **CIMD Client ID**.

   For **Value**, enter the HTTPS URL of the metadata document that your MCP client hosts. The URL must meet the [CIMD document requirements](#cimd-document-requirements).

   For example: `https://mcp.example.com/.well-known/oauth/client-metadata.json`

6. Click **Save**.

   Aembit displays the new Client Workload on the **Client Workloads** page.

## Configure a CIMD Client ID Client Workload using the Aembit Terraform provider

To configure this Client Workload with the [Aembit Terraform Provider](https://registry.terraform.io/providers/Aembit/aembit/latest), set the `cimdClientId` identity type on the `aembit_client_workload` resource.

The following configuration is an example. For the authoritative, current argument reference, see the [`aembit_client_workload` resource](https://registry.terraform.io/providers/Aembit/aembit/latest/docs/resources/client_workload) in the Terraform Registry.

```hcl
provider "aembit" {
}


resource "aembit_client_workload" "mcp_client" {
  name      = "<Desired Client Workload Name>"
  is_active = true
  identities = [
    {
      type  = "cimdClientId"
      value = "https://mcp.example.com/.well-known/oauth/client-metadata.json"
    }
  ]
}
```

For `value`, use the HTTPS URL of the metadata document that your MCP client hosts. The URL must meet the [CIMD document requirements](#cimd-document-requirements).
