---
type: reference
title: "GET /api/v1/content-security/{csId}/mcp-tool-rules/{ruleId}"
description: "Get an MCP Tool Access Rule"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["mcp-tool-access-rule"]
timestamp: 2026-09-22T20:22:25-07:00
---

# GET /api/v1/content-security/{csId}/mcp-tool-rules/{ruleId}

Get an MCP Tool Access Rule identified by its ID.

**Operation ID:** `get-mcp-tool-rule`

**Parameters:**

- **csId** (path) *(required)*: string (uuid) - ID of Content Security
- **ruleId** (path) *(required)*: string (uuid) - ID of MCP Tool Access Rule
- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Responses:**

- **200**: MCP Tool Access Rule
  - `application/json`: [McpToolAccessControlRuleDTO](../schemas/mcp-tool-access-control-rule-dto.md)
- **204**: MCP Tool Access Rule Not Found
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/content-security/{csId}/mcp-tool-rules/{ruleId}"
```
