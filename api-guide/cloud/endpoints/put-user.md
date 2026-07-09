---
type: reference
title: "PUT /api/v1/users/{id}"
description: "Update a User"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [user]
timestamp: 2025-08-28T08:38:37-04:00
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
