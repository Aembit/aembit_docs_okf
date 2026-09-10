---
type: reference
title: "PUT /api/v1/roles"
description: "Update a Role"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["role"]
timestamp: 2026-07-28T14:42:14-04:00
---

# PUT /api/v1/roles

Update a Role.

**Operation ID:** `put-role`

**Request Body:**

RoleDTO

- `application/json`: [RoleDTO](../schemas/role-dto.md)

**Responses:**

- **200**: Updated Role
  - `application/json`: [RoleDTO](../schemas/role-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X PUT \
  -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/roles"
```
