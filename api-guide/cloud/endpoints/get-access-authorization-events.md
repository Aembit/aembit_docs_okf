---
type: reference
title: "GET /api/v1/authorization-events"
description: "Get a page of Access Authorization Events"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [access-authorization-event]
timestamp: 2025-08-28T08:38:37-04:00
---

# GET /api/v1/authorization-events

Get a page of Access Authorization Events.

**Operation ID:** `get-access-authorization-events`

**Parameters:**

- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)
- **page** (query) *(optional)*: integer (int32)
- **per-page** (query) *(optional)*: integer (int32)
- **order** (query) *(optional)*: string
- **search** (query) *(optional)*: string
- **span-last-hours** (query) *(optional)*: integer (int32)
- **severity** (query) *(optional)*: string
- **event-type** (query) *(optional)*: string

**Responses:**

- **200**: Page of Access Authorization Events
  - `application/json`: [AuthorizationEventListDTO](../schemas/authorization-event-list-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/authorization-events?page=example-value&per-page=example-value&order=example-value&search=example-value&span-last-hours=example-value&severity=example-value&event-type=example-value"
```
