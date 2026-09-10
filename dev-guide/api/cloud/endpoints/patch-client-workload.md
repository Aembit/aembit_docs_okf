---
type: reference
title: "PATCH /api/v1/client-workloads/{id}"
description: "Patch a Client Workload"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["client-workload"]
timestamp: 2026-07-28T14:42:14-04:00
---

# PATCH /api/v1/client-workloads/{id}

Patch a Client Workload.

**Operation ID:** `patch-client-workload`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Client Workload
- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Request Body:**

ClientWorkloadPatchDTO

- `application/json`: [ClientWorkloadPatchDTO](../schemas/client-workload-patch-dto.md)

**Responses:**

- **200**: Patched Client Workload
  - `application/json`: [ClientWorkloadExternalDTO](../schemas/client-workload-external-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X PATCH \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/client-workloads/{id}"
```
