---
type: reference
title: "PATCH /api/v1/users/{id}"
description: "Patch a User"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["user"]
timestamp: 2026-07-28T14:42:14-04:00
---

# PATCH /api/v1/users/{id}

Patch a User identified by its ID.

**Operation ID:** `patch-user`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of User

**Request Body:**

UserPatchDTO

- `application/json`: [UserPatchDTO](../schemas/user-patch-dto.md)

**Responses:**

- **200**: Patched User
  - `application/json`: [UserDTO](../schemas/user-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X PATCH \
  -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/users/{id}"
```
