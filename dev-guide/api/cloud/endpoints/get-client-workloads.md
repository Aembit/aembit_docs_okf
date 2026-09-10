---
type: reference
title: "GET /api/v1/client-workloads"
description: "Get a page of Client Workloads"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["client-workload"]
timestamp: 2026-07-28T14:42:14-04:00
---

# GET /api/v1/client-workloads

Get a page of Client Workloads.

**Operation ID:** `get-client-workloads`

**Parameters:**

- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)
- **page** (query) *(optional)*: integer (int32)
- **per-page** (query) *(optional)*: integer (int32)
- **filter** (query) *(optional)*: string
- **order** (query) *(optional)*: string
- **group-by** (query) *(optional)*: string

**Responses:**

- **200**: Page of Client Workloads
  - `application/json`: [ClientWorkloadListDTO](../schemas/client-workload-list-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/client-workloads?page=example-value&per-page=example-value&filter=example-value&order=example-value&group-by=example-value"
```
