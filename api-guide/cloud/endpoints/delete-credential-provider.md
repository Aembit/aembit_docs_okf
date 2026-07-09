---
type: reference
title: "DELETE /api/v1/credential-providers/{id}"
description: "Delete a Credential Provider"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [credential-provider-deprecated]
timestamp: 2025-08-28T08:38:37-04:00
---

# DELETE /api/v1/credential-providers/{id}

Delete a Credential Provider identified by its ID.

**Operation ID:** `delete-credential-provider`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Credential Provider
- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Responses:**

- **204**: Deleted Credential Provider
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X DELETE \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/credential-providers/{id}"
```
