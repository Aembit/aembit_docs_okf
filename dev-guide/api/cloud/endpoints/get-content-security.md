---
type: reference
title: "GET /api/v1/content-security/{id}"
description: "Get a Content Security"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["content-security"]
timestamp: 2026-07-28T14:42:14-04:00
---

# GET /api/v1/content-security/{id}

Get a Content Security identified by its ID.

**Operation ID:** `get-content-security`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Content Security

**Responses:**

- **200**: Content Security
  - `application/json`: any
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/content-security/{id}"
```
