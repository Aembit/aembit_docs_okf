---
type: reference
title: "DELETE /api/v1/discovery-integrations/{id}"
description: "Delete an Integration"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["discovery-integration"]
timestamp: 2026-07-28T14:42:14-04:00
---

# DELETE /api/v1/discovery-integrations/{id}

Delete an Integration as identified by its ID.

**Operation ID:** `delete-discovery-integration`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Integration

**Responses:**

- **200**: Successfully deleted Integration
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X DELETE \
  -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/discovery-integrations/{id}"
```
