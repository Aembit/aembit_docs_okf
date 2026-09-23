---
type: reference
title: "GET /api/v1/content-security/{csId}/mcp-tool-rules"
description: "Get a page of MCP Tool Access Rules"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["mcp-tool-access-rule"]
timestamp: 2026-09-22T20:22:25-07:00
---

# GET /api/v1/content-security/{csId}/mcp-tool-rules

Get a page of MCP Tool Access Rules.

**Operation ID:** `get-mcp-tool-rules`

**Parameters:**

- **csId** (path) *(required)*: string (uuid) - ID of Content Security
- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)
- **page** (query) *(optional)*: integer (int32)
- **per-page** (query) *(optional)*: integer (int32)
- **filter** (query) *(optional)*: string
- **order** (query) *(optional)*: string

**Responses:**

- **200**: Page of MCP Tool Access Rules
  - `application/json`: [McpToolAccessControlRuleDTOListDTO](../schemas/mcp-tool-access-control-rule-dto-list-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/content-security/{csId}/mcp-tool-rules?page=example-value&per-page=example-value&filter=example-value&order=example-value"
```
