---
type: reference
title: "GET /api/v1/workload-events/{id}"
description: "Get a Workload Event"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [workload-event]
timestamp: 2025-08-28T08:38:37-04:00
---

# GET /api/v1/workload-events/{id}

Get a Workload Event.

**Operation ID:** `get-workload-event`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Workload Event
- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Responses:**

- **200**: Workload Event
  - `application/json`: [EventDTO](../schemas/event-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/workload-events/{id}"
```
