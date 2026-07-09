---
type: reference
title: "POST /api/v1/roles"
description: "Create a new Role"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [role]
timestamp: 2025-08-28T08:38:37-04:00
---

# POST /api/v1/roles

Create a new Role.

**Operation ID:** `post-role`

**Request Body:**

RoleDTO

- `application/json`: [RoleDTO](../schemas/role-dto.md)

**Responses:**

- **201**: Created Role
  - `application/json`: [RoleDTO](../schemas/role-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X POST \
  -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/roles"
```
