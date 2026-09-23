---
type: reference
title: "GET /api/v1/server-workloads/{id}"
description: "Get a Server Workload"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["server-workload"]
timestamp: 2026-09-22T20:22:25-07:00
---

# GET /api/v1/server-workloads/{id}

Get a Server Workload identified by its ID.

**Operation ID:** `get-server-workload`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Server Workload
- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Responses:**

- **200**: Server Workload
  - `application/json`: [ServerWorkloadExternalDTO](../schemas/server-workload-external-dto.md)
- **204**: Server Workload Not Found
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/server-workloads/{id}"
```
