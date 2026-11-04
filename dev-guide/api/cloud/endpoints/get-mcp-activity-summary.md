---
type: reference
title: "GET /api/v1/mcp-activity"
description: "Get MCP Access Activity Summary"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["mcp-activity"]
timestamp: 2026-11-03T18:06:18-08:00
---

# GET /api/v1/mcp-activity

Retrieves paginated MCP access activity summary items across client transaction groups. Use isException=true for exceptions and isException=false for non-exceptions.

**Operation ID:** `get-mcp-activity-summary`

**Parameters:**

- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)
- **page** (query) *(optional)*: integer (int32)
- **per-page** (query) *(optional)*: integer (int32)
- **order** (query) *(optional)*: string
- **search** (query) *(optional)*: string
- **span-last-hours** (query) *(optional)*: integer (int32)
- **span-last-minutes** (query) *(optional)*: integer (int64)
- **start-date** (query) *(optional)*: string (date-time)
- **end-date** (query) *(optional)*: string (date-time)
- **user** (query) *(optional)*: string
- **user-id** (query) *(optional)*: string
- **mcp-client** (query) *(optional)*: string
- **mcp-server** (query) *(optional)*: string
- **mcp-method** (query) *(optional)*: string
- **target** (query) *(optional)*: string
- **category** (query) *(optional)*: string
- **exception** (query) *(optional)*: string
- **isException** (query) *(optional)*: boolean

**Responses:**

- **200**: Paginated MCP Access Activity Summary
  - `application/json`: [McpActivityReportItemDTOListDTO](../schemas/mcp-activity-report-item-dto-list-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **403**: Forbidden
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/mcp-activity?page=example-value&per-page=example-value&order=example-value&search=example-value&span-last-hours=example-value&span-last-minutes=example-value&start-date=example-value&end-date=example-value&user=example-value&user-id=example-value&mcp-client=example-value&mcp-server=example-value&mcp-method=example-value&target=example-value&category=example-value&exception=example-value&isException=example-value"
```
