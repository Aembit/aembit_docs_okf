---
type: reference
title: "GET /api/v1/roles/{id}"
description: "Get a Role"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["role"]
timestamp: 2026-09-22T20:22:25-07:00
---

# GET /api/v1/roles/{id}

Get a Role identified by its ID.

**Operation ID:** `get-role`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Role

**Responses:**

- **200**: Role
  - `application/json`: [RoleDTO](../schemas/role-dto.md)
- **204**: Role Not Found
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/roles/{id}"
```
