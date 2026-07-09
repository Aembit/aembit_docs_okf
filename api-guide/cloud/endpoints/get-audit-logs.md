---
type: reference
title: "GET /api/v1/audit-logs"
description: "Get a page of Audit Log events"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [audit-log]
timestamp: 2025-08-28T08:38:37-04:00
---

# GET /api/v1/audit-logs

Get a page of Audit Log events.

**Operation ID:** `get-audit-logs`

**Parameters:**

- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)
- **page** (query) *(optional)*: integer (int32)
- **per-page** (query) *(optional)*: integer (int32)
- **order** (query) *(optional)*: string
- **search** (query) *(optional)*: string
- **span-last-days** (query) *(optional)*: integer (int32)
- **category** (query) *(optional)*: string
- **severity** (query) *(optional)*: string

**Responses:**

- **200**: Page of Audit Logs
  - `application/json`: [AuditLogListDTO](../schemas/audit-log-list-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/audit-logs?page=example-value&per-page=example-value&order=example-value&search=example-value&span-last-days=example-value&category=example-value&severity=example-value"
```
