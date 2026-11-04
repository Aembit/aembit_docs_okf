---
type: reference
title: "GET /api/v1/access-scenarios/by-policy/{policyId}"
description: "Get Access Scenario for a Policy"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["access-scenario"]
timestamp: 2026-11-03T18:06:18-08:00
---

# GET /api/v1/access-scenarios/by-policy/{policyId}

Get the Access Scenario associated with an Access Policy ID.

**Operation ID:** `get-access-scenario-by-policy`

**Parameters:**

- **policyId** (path) *(required)*: string (uuid) - ID of Access Policy
- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Responses:**

- **200**: Access Scenario
  - `application/json`: [GetScenarioDTO](../schemas/get-scenario-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **404**: Access Scenario Not Found
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/access-scenarios/by-policy/{policyId}"
```
