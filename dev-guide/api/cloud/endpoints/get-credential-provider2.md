---
type: reference
title: "GET /api/v2/credential-providers/{id}"
description: "Get a Credential Provider"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["credential-provider-v2"]
timestamp: 2026-09-22T20:22:25-07:00
---

# GET /api/v2/credential-providers/{id}

Get a Credential Provider identified by its ID.

**Operation ID:** `get-credential-provider2`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Credential Provider
- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Responses:**

- **200**: Credential Provider
  - `application/json`: any
- **204**: Credential Provider Not Found
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v2/credential-providers/{id}"
```
