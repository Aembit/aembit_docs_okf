---
type: reference
title: "DELETE /api/v1/sso-idps/{id}"
description: "Delete a SSO Identity Provider"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["sso-identity-provider"]
timestamp: 2026-09-25T10:20:24-07:00
---

# DELETE /api/v1/sso-idps/{id}

Delete a SSO Identity Provider identified by its ID.

**Operation ID:** `delete-identity-provider`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of SSO Identity Provider
- **revokeSessions** (query) *(optional)*: boolean - Whether to revoke all active sessions associated with the Identity Provider

**Responses:**

- **204**: Successfully deleted SSO Identity Provider
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X DELETE \
  -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/sso-idps/{id}?revokeSessions=example-value"
```
