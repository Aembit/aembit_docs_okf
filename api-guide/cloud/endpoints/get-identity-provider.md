---
type: reference
title: "GET /api/v1/sso-idps/{id}"
description: "Get a SSO Identity Provider"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [sso-identity-provider]
timestamp: 2025-08-28T08:38:37-04:00
---

# GET /api/v1/sso-idps/{id}

Get a SSO Identity Provider identified by its ID.

**Operation ID:** `get-identity-provider`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of SSO Identity Provider

**Responses:**

- **200**: SSO Identity Provider
  - `application/json`: [SSOIdentityProviderDTO](../schemas/sso-identity-provider-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/sso-idps/{id}"
```
