---
type: reference
title: "DELETE /api/v1/trust-providers/{tpId}/secrets/{secretId}"
description: "Delete a Trust Provider Secret"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["trust-provider-secret"]
timestamp: 2026-09-22T20:22:25-07:00
---

# DELETE /api/v1/trust-providers/{tpId}/secrets/{secretId}

Delete a Trust Provider Secret identified by its ID.

**Operation ID:** `delete-trust-provider-secret`

**Parameters:**

- **tpId** (path) *(required)*: string (uuid) - ID of Trust Provider
- **secretId** (path) *(required)*: string (uuid) - ID of Trust Provider Secret
- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Responses:**

- **204**: Successfully deleted Trust Provider Secret
- **400**: Bad Request
- **401**: Not Authenticated
- **404**: Not Found
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X DELETE \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/trust-providers/{tpId}/secrets/{secretId}"
```
