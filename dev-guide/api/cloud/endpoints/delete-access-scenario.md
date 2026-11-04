---
type: reference
title: "DELETE /api/v1/access-scenarios/{id}"
description: "Delete an Access Scenario"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["access-scenario"]
timestamp: 2026-11-03T18:06:18-08:00
---

# DELETE /api/v1/access-scenarios/{id}

Delete an Access Scenario identified by its ID.

**Operation ID:** `delete-access-scenario`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Access Scenario
- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Responses:**

- **204**: Deleted the Access Scenario
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X DELETE \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/access-scenarios/{id}"
```
