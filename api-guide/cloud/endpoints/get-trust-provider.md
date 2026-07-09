---
type: reference
title: "GET /api/v1/trust-providers/{id}"
description: "Get a Trust Provider"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [trust-provider]
timestamp: 2025-08-28T08:38:37-04:00
---

# GET /api/v1/trust-providers/{id}

Get a Trust Provider identified by its ID.

**Operation ID:** `get-trust-provider`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Trust Provider
- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Responses:**

- **200**: Trust Provider
  - `application/json`: [TrustProviderDTO](../schemas/trust-provider-dto.md)
- **204**: Trust Provider Not Found
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/trust-providers/{id}"
```
