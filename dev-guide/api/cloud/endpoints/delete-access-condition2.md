---
type: reference
title: "DELETE /api/v2/access-conditions/{id}"
description: "Delete a single Access Condition"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["access-condition-v2"]
timestamp: 2026-09-22T20:22:25-07:00
---

# DELETE /api/v2/access-conditions/{id}

Delete a specific Access Condition identified by its ID.

**Operation ID:** `delete-access-condition2`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Access Condition
- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Responses:**

- **204**: Deleted the Access Condition
- **400**: Bad Request
- **401**: Not Authenticated
- **404**: Not Found
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X DELETE \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v2/access-conditions/{id}"
```
