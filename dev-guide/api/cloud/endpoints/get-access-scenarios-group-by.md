---
type: reference
title: "GET /api/v1/access-scenarios/group-by"
description: "Get scenario count grouped by field"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["access-scenario"]
timestamp: 2026-11-03T18:06:18-08:00
---

# GET /api/v1/access-scenarios/group-by

Retrieve access scenario counts grouped by a supported field ('isactive').

**Operation ID:** `get-access-scenarios-group-by`

**Parameters:**

- **fields** (query) *(required)*: string - Field to group by ('isactive')
- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Responses:**

- **200**: Grouped Access Scenarios
  - `application/json`: [GroupByDTO](../schemas/group-by-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/access-scenarios/group-by?fields=example-value"
```
