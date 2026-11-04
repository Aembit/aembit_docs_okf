---
type: reference
title: "GET /api/v1/mcp-activity/request-history"
description: "Get MCP Request History"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["mcp-activity"]
timestamp: 2026-11-03T18:06:18-08:00
---

# GET /api/v1/mcp-activity/request-history

Retrieves paginated request execution history for an activity or exception pattern.

**Operation ID:** `get-mcp-request-history`

**Parameters:**

- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)
- **page** (query) *(optional)*: integer (int32)
- **per-page** (query) *(optional)*: integer (int32)
- **order** (query) *(optional)*: string
- **span-last-hours** (query) *(optional)*: integer (int32)
- **span-last-minutes** (query) *(optional)*: integer (int64)
- **start-date** (query) *(optional)*: string (date-time)
- **end-date** (query) *(optional)*: string (date-time)
- **user** (query) *(optional)*: string
- **mcp-client** (query) *(optional)*: string
- **mcp-server** (query) *(optional)*: string
- **mcp-method** (query) *(optional)*: string
- **target** (query) *(optional)*: string
- **exception-category** (query) *(optional)*: string
- **exception-code** (query) *(optional)*: string
- **category** (query) *(optional)*: string
- **exception** (query) *(optional)*: string
- **isException** (query) *(optional)*: boolean

**Responses:**

- **200**: Paginated MCP Request History
  - `application/json`: [McpRequestHistoryItemDTOListDTO](../schemas/mcp-request-history-item-dto-list-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **403**: Forbidden
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/mcp-activity/request-history?page=example-value&per-page=example-value&order=example-value&span-last-hours=example-value&span-last-minutes=example-value&start-date=example-value&end-date=example-value&user=example-value&mcp-client=example-value&mcp-server=example-value&mcp-method=example-value&target=example-value&exception-category=example-value&exception-code=example-value&category=example-value&exception=example-value&isException=example-value"
```
