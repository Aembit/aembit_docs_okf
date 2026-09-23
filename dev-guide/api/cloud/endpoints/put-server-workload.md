---
type: reference
title: "PUT /api/v1/server-workloads"
description: "Update a Server Workload"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["server-workload"]
timestamp: 2026-09-22T20:22:25-07:00
---

# PUT /api/v1/server-workloads

Update a Server Workload.

**Operation ID:** `put-server-workload`

**Parameters:**

- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Request Body:**

ServerWorkloadExternalDTO

- `application/json`: [ServerWorkloadExternalDTO](../schemas/server-workload-external-dto.md)

**Responses:**

- **200**: Updated Server Workload
  - `application/json`: [ServerWorkloadExternalDTO](../schemas/server-workload-external-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X PUT \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/server-workloads"
```
