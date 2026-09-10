---
type: reference
title: "PUT /api/v1/content-security"
description: "Update a Content Security"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["content-security"]
timestamp: 2026-07-28T14:42:14-04:00
---

# PUT /api/v1/content-security

Update a Content Security.

**Operation ID:** `put-content-security`

**Request Body:**

ContentSecurityDTO

- `application/json`: any

**Responses:**

- **200**: Updated Content Security
  - `application/json`: any
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X PUT \
  -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/content-security"
```
