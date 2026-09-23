---
type: reference
title: "POST /api/v1/content-security/{csId}/mcp-tool-rules"
description: "Create or Update an MCP Tool Access Rule"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["mcp-tool-access-rule"]
timestamp: 2026-09-22T20:22:25-07:00
---

# POST /api/v1/content-security/{csId}/mcp-tool-rules

Create or Update an MCP Tool Access Rule.

**Operation ID:** `post-mcp-tool-rule`

**Parameters:**

- **csId** (path) *(required)*: string (uuid) - ID of Content Security
- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Request Body:**

McpToolAccessControlRuleDTO

- `application/json`: [McpToolAccessControlRuleDTO](../schemas/mcp-tool-access-control-rule-dto.md)

**Responses:**

- **200**: Updated MCP Tool Access Rule
  - `application/json`: [McpToolAccessControlRuleDTO](../schemas/mcp-tool-access-control-rule-dto.md)
- **201**: Created MCP Tool Access Rule
  - `application/json`: [McpToolAccessControlRuleDTO](../schemas/mcp-tool-access-control-rule-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X POST \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/content-security/{csId}/mcp-tool-rules"
```
