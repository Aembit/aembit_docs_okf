---
type: reference
title: "GET /api/v1/authorization-events/{id}"
description: "Get an Access Authorization Event"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["access-authorization-event"]
timestamp: 2026-07-28T14:42:14-04:00
---

# GET /api/v1/authorization-events/{id}

Get an Access Authorization Event identified by its ID.

**Operation ID:** `get-access-authorization-event`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Access Authorization Event
- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Responses:**

- **200**: Access Authorization Event
  - `application/json`: [AuthorizationEventDTO](../schemas/authorization-event-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/authorization-events/{id}"
```
