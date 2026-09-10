---
type: reference
title: "DELETE /api/v1/resource-sets/{id}"
description: "Delete a Resource Set"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["resource-set"]
timestamp: 2026-07-28T14:42:14-04:00
---

# DELETE /api/v1/resource-sets/{id}

Delete a Resource Set identified by its ID.

**Operation ID:** `delete-resource-set-integration`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Resource Set

**Responses:**

- **204**: Successfully deleted Resource Set
- **400**: Bad Request
- **401**: Not Authenticated
- **404**: Not Found
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X DELETE \
  -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/resource-sets/{id}"
```
