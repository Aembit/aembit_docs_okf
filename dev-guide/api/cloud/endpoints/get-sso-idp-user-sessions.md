---
type: reference
title: "GET /api/v1/sso-idps/{id}/user-sessions"
description: "Get active and revoked user sessions for an SSO Identity Provider"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["sso-identity-provider"]
timestamp: 2026-09-22T20:22:25-07:00
---

# GET /api/v1/sso-idps/{id}/user-sessions

Get all MCP user sessions associated with an SSO Identity Provider.

**Operation ID:** `get-sso-idp-user-sessions`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of SSO Identity Provider

**Responses:**

- **200**: List of user sessions
  - `application/json`: Array of [SsoIdpUserSessionDTO](../schemas/sso-idp-user-session-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **404**: Not Found
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/sso-idps/{id}/user-sessions"
```
