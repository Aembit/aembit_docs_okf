---
type: reference
title: "POST /api/v1/users"
description: "Create a User"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [user]
timestamp: 2025-08-28T08:38:37-04:00
---

# POST /api/v1/users

Create a User.

**Operation ID:** `post-user`

**Request Body:**

UserDTO

- `application/json`: [UserDTO](../schemas/user-dto.md)

**Responses:**

- **201**: Created User
  - `application/json`: [UserDTO](../schemas/user-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X POST \
  -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/users"
```
