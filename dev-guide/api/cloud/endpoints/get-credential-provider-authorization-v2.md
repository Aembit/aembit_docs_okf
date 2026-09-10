---
type: reference
title: "GET /api/v2/credential-providers/{id}/authorize"
description: "Get a Credential Provider Authorization URL"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["credential-provider-v2"]
timestamp: 2026-07-28T14:42:14-04:00
---

# GET /api/v2/credential-providers/{id}/authorize

Get a Credential Provider Authorization URL identified by the Credential Provider ID.

**Operation ID:** `get-credential-provider-authorization-v2`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Credential Provider
- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Responses:**

- **302**: Redirects to the Credential Provider Authorization URL
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v2/credential-providers/{id}/authorize"
```
