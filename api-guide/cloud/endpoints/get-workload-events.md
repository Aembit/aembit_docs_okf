---
type: reference
title: "GET /api/v1/workload-events"
description: "Get a page of Workload Events"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [workload-event]
timestamp: 2025-08-28T08:38:37-04:00
---

# GET /api/v1/workload-events

Get a page of Workload Events.

**Operation ID:** `get-workload-events`

**Parameters:**

- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)
- **page** (query) *(optional)*: integer (int32)
- **per-page** (query) *(optional)*: integer (int32)
- **order** (query) *(optional)*: string
- **search** (query) *(optional)*: string
- **span-last-hours** (query) *(optional)*: integer (int32)
- **application-protocol** (query) *(optional)*: string
- **severity** (query) *(optional)*: string
- **source-workload** (query) *(optional)*: Array of string (uuid)
- **target-workload** (query) *(optional)*: Array of string (uuid)

**Responses:**

- **200**: Page of Workload Events
  - `application/json`: [EventListDTO](../schemas/event-list-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/workload-events?page=example-value&per-page=example-value&order=example-value&search=example-value&span-last-hours=example-value&application-protocol=example-value&severity=example-value&source-workload=example-value&target-workload=example-value"
```
