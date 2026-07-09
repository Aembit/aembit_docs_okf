---
type: reference
title: "GET /api/v1/client-workloads/{id}"
description: "Get a Client Workload"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [client-workload]
timestamp: 2025-08-28T08:38:37-04:00
---

# GET /api/v1/client-workloads/{id}

Get a Client Workload identified by its ID.

**Operation ID:** `get-client-workload`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Client Workload
- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Responses:**

- **200**: Client Workload
  - `application/json`: [ClientWorkloadExternalDTO](../schemas/client-workload-external-dto.md)
- **204**: Client Workload Not Found
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/client-workloads/{id}"
```
