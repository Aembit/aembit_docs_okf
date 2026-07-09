---
type: reference
title: "GET /api/v1/audit-logs/{id}"
description: "Get an Audit Log event"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [audit-log]
timestamp: 2025-08-28T08:38:37-04:00
---

# GET /api/v1/audit-logs/{id}

Get an Audit Log event identified by its ID.

**Operation ID:** `get-audit-log`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Audit Log
- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Responses:**

- **200**: Audit Log
  - `application/json`: [AuditLogDTO](../schemas/audit-log-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/audit-logs/{id}"
```
