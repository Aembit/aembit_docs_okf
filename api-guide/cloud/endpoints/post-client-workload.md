---
type: reference
title: "POST /api/v1/client-workloads"
description: "Create a Client Workload"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [client-workload]
timestamp: 2025-08-28T08:38:37-04:00
---

# POST /api/v1/client-workloads

Create a Client Workload.

**Operation ID:** `post-client-workload`

**Parameters:**

- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Request Body:**

ClientWorkloadExternalDTO

- `application/json`: [ClientWorkloadExternalDTO](../schemas/client-workload-external-dto.md)

**Responses:**

- **204**: Created Client Workload
  - `application/json`: [ClientWorkloadExternalDTO](../schemas/client-workload-external-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X POST \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/client-workloads"
```
