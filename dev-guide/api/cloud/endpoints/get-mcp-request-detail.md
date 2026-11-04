---
type: reference
title: "GET /api/v1/mcp-activity/requests/{correlationId}"
description: "Get MCP Request Detail"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["mcp-activity"]
timestamp: 2026-11-03T18:06:18-08:00
---

# GET /api/v1/mcp-activity/requests/{correlationId}

Retrieves correlated details, per-server outcomes, and relevant events timeline for a specific MCP request correlation ID.

**Operation ID:** `get-mcp-request-detail`

**Parameters:**

- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)
- **correlationId** (path) *(required)*: string

**Responses:**

- **200**: Correlated MCP Request Details
  - `application/json`: [McpRequestDetailDTO](../schemas/mcp-request-detail-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **403**: Forbidden
- **404**: Not Found
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/mcp-activity/requests/{correlationId}"
```
