---
type: reference
title: "GET /api/v1/access-policies/getByWorkloadIds/{clientWorkloadId}/{serverWorkloadId}"
description: "Get the identified Access Policy"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [access-policy-deprecated]
timestamp: 2025-08-28T08:38:37-04:00
---

# GET /api/v1/access-policies/getByWorkloadIds/{clientWorkloadId}/{serverWorkloadId}

Get the Access Policy identified by a Client and Server Workload.

**Operation ID:** `get-access-policy-by-workloads`

**Parameters:**

- **clientWorkloadId** (path) *(required)*: string (uuid) - ID of Client Workload
- **serverWorkloadId** (path) *(required)*: string (uuid) - ID of Server Workload
- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Responses:**

- **200**: Access Policy
  - `application/json`: [PolicyExternalDTO](../schemas/policy-external-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/access-policies/getByWorkloadIds/{clientWorkloadId}/{serverWorkloadId}"
```
