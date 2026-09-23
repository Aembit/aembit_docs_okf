---
type: reference
title: "PUT /api/v1/users/{id}"
description: "Update a User"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["user"]
timestamp: 2026-09-22T20:22:25-07:00
---

# PUT /api/v1/users/{id}

Update a User.

**Operation ID:** `put-user`

**Parameters:**

- **id** (path) *(required)*: string

**Request Body:**

UserDTO

- `application/json`: [UserDTO](../schemas/user-dto.md)

**Responses:**

- **200**: User
  - `application/json`: [UserDTO](../schemas/user-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X PUT \
  -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/users/{id}"
```
