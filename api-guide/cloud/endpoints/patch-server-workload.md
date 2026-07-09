---
type: reference
title: "PATCH /api/v1/server-workloads/{id}"
description: "Patch a Server Workload"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [server-workload]
timestamp: 2025-08-28T08:38:37-04:00
---

# PATCH /api/v1/server-workloads/{id}

Patch a Server Workload.

**Operation ID:** `patch-server-workload`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Server Workload
- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Request Body:**

EntityPatchDTO

- `application/json`: [EntityPatchDTO](../schemas/entity-patch-dto.md)

**Responses:**

- **200**: Patched Server Workload
  - `application/json`: [ServerWorkloadExternalDTO](../schemas/server-workload-external-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X PATCH \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/server-workloads/{id}"
```
