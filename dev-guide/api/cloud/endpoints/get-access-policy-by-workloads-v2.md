---
type: reference
title: "GET /api/v2/access-policies/getByWorkloadIds/{clientWorkloadId}/{serverWorkloadId}"
description: "Get the identified Access Policy"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["access-policy-v2"]
timestamp: 2026-09-22T20:22:25-07:00
---

# GET /api/v2/access-policies/getByWorkloadIds/{clientWorkloadId}/{serverWorkloadId}

Get the Access Policy identified by a Client and Server Workload.

**Operation ID:** `get-access-policy-by-workloads-v2`

**Parameters:**

- **clientWorkloadId** (path) *(required)*: string (uuid) - ID of Client Workload
- **serverWorkloadId** (path) *(required)*: string (uuid) - ID of Server Workload

**Responses:**

- **200**: Access Policy
  - `application/json`: [GetPolicyDTO](../schemas/get-policy-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v2/access-policies/getByWorkloadIds/{clientWorkloadId}/{serverWorkloadId}"
```
