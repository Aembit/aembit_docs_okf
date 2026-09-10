---
type: reference
title: "PATCH /api/v1/content-security/{id}"
description: "Patch a Content Security"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["content-security"]
timestamp: 2026-07-28T14:42:14-04:00
---

# PATCH /api/v1/content-security/{id}

Patch a Content Security identified by its ID.

**Operation ID:** `patch-content-security`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Content Security

**Request Body:**

ContentSecurityPatchDTO

- `application/json`: [ContentSecurityPatchDTO](../schemas/content-security-patch-dto.md)

**Responses:**

- **200**: Patched Content Security
  - `application/json`: any
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X PATCH \
  -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/content-security/{id}"
```
