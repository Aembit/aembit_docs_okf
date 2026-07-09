---
type: reference
title: "DELETE /api/v1/client-workloads/{id}"
description: "Delete a Client Workload"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [client-workload]
timestamp: 2025-08-28T08:38:37-04:00
---

# DELETE /api/v1/client-workloads/{id}

Delete a Client Workload identified by its ID.

**Operation ID:** `delete-client-workload`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Client Workload
- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Responses:**

- **204**: Successfully deleted Client Workload
- **400**: Bad Request
- **401**: Not Authenticated
- **404**: Not Found
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X DELETE \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/client-workloads/{id}"
```
