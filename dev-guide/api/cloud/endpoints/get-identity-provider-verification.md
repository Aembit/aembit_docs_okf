---
type: reference
title: "GET /api/v1/sso-idps/{id}/verification"
description: "Verify the SSO Identity Provider"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["sso-identity-provider"]
timestamp: 2026-07-28T14:42:14-04:00
---

# GET /api/v1/sso-idps/{id}/verification

Verify the SSO Identity Provider has all necessary configuration data.

**Operation ID:** `get-identity-provider-verification`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of SSO Identity Provider

**Responses:**

- **200**: SSO Identity Provider verification
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/sso-idps/{id}/verification"
```
