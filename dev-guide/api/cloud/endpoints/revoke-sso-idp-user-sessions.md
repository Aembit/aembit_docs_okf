---
type: reference
title: "POST /api/v1/sso-idps/{id}/user-sessions/revoke"
description: "Revoke user sessions for an SSO Identity Provider"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["sso-identity-provider"]
timestamp: 2026-09-22T20:22:25-07:00
---

# POST /api/v1/sso-idps/{id}/user-sessions/revoke

Revoke a single user session by grantKey or all sessions for a user by subjectId.

**Operation ID:** `revoke-sso-idp-user-sessions`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of SSO Identity Provider

**Request Body:**

Revocation request payload

- `application/json`: [RevokeUserSessionRequestDTO](../schemas/revoke-user-session-request-dto.md)

**Responses:**

- **200**: Sessions revoked successfully
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)
- **400**: Bad Request
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)
- **401**: Not Authenticated
- **404**: Not Found
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X POST \
  -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/sso-idps/{id}/user-sessions/revoke"
```
