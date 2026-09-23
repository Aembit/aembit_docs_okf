---
type: reference
title: "DELETE /api/v1/server-workloads/{id}"
description: "Delete a Server Workload"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["server-workload"]
timestamp: 2026-09-22T20:22:25-07:00
---

# DELETE /api/v1/server-workloads/{id}

Delete a Server Workload identified by its ID.

**Operation ID:** `delete-server-workload`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Server Workload
- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Responses:**

- **204**: Successfully deleted Server Workload
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X DELETE \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/server-workloads/{id}"
```
