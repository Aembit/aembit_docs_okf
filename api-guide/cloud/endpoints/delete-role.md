---
type: reference
title: "DELETE /api/v1/roles/{id}"
description: "Delete a Role"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [role]
timestamp: 2025-08-28T08:38:37-04:00
---

# DELETE /api/v1/roles/{id}

Delete a Role identified by its ID.

**Operation ID:** `delete-role`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Role

**Responses:**

- **204**: Successfully deleted Role
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X DELETE \
  -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/roles/{id}"
```
