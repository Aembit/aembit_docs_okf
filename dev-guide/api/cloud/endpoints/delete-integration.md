---
type: reference
title: "DELETE /api/v1/integrations/{id}"
description: "Delete an Integration"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["integration"]
timestamp: 2026-07-28T14:42:14-04:00
---

# DELETE /api/v1/integrations/{id}

Delete an Integration as identified by its ID.

**Operation ID:** `delete-integration`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Integration
- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Responses:**

- **200**: Successfully deleted Integration
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X DELETE \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/integrations/{id}"
```
