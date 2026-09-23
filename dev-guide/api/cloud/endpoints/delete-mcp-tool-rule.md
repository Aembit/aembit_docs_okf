---
type: reference
title: "DELETE /api/v1/content-security/{csId}/mcp-tool-rules/{ruleId}"
description: "Delete an MCP Tool Access Rule"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["mcp-tool-access-rule"]
timestamp: 2026-09-22T20:22:25-07:00
---

# DELETE /api/v1/content-security/{csId}/mcp-tool-rules/{ruleId}

Delete an MCP Tool Access Rule identified by its ID.

**Operation ID:** `delete-mcp-tool-rule`

**Parameters:**

- **csId** (path) *(required)*: string (uuid) - ID of Content Security
- **ruleId** (path) *(required)*: string (uuid) - ID of MCP Tool Access Rule
- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Responses:**

- **204**: Successfully deleted MCP Tool Access Rule
- **400**: Bad Request
- **401**: Not Authenticated
- **404**: Not Found
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X DELETE \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/content-security/{csId}/mcp-tool-rules/{ruleId}"
```
