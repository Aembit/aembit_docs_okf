---
type: reference
title: "DELETE /api/v2/credential-providers/{id}"
description: "Delete a Credential Provider"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["credential-provider-v2"]
timestamp: 2026-07-28T14:42:14-04:00
---

# DELETE /api/v2/credential-providers/{id}

Delete a Credential Provider identified by its ID.

**Operation ID:** `delete-credential-provider2`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Credential Provider
- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Responses:**

- **204**: Deleted Credential Provider
- **400**: Bad Request
- **401**: Not Authenticated
- **404**: Not Found
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X DELETE \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v2/credential-providers/{id}"
```
