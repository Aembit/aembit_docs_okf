---
type: reference
title: "PATCH /api/v1/sso-idps/{id}"
description: "Patch a SSO Identity Provider"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["sso-identity-provider"]
timestamp: 2026-07-28T14:42:14-04:00
---

# PATCH /api/v1/sso-idps/{id}

Patch a SSO Identity Provider identified by its ID.

**Operation ID:** `patch-identity-provider`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of SSO Identity Provider

**Request Body:**

SSOIdentityProviderPatchDTO

- `application/json`: [SSOIdentityProviderPatchDTO](../schemas/sso-identity-provider-patch-dto.md)

**Responses:**

- **200**: Patched SSO Identity Provider
  - `application/json`: any
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X PATCH \
  -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/sso-idps/{id}"
```
