---
type: reference
title: "GET /api/v1/sso-idps/{id}/user-sessions"
description: "Get active and revoked user sessions for an SSO Identity Provider"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["sso-identity-provider"]
timestamp: 2026-10-13T16:56:08-07:00
---

# GET /api/v1/sso-idps/{id}/user-sessions

Get paginated MCP user sessions associated with an SSO Identity Provider.

**Operation ID:** `get-sso-idp-user-sessions`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of SSO Identity Provider
- **page** (query) *(optional)*: integer (int32) - Current page number (1-based)
- **per-page** (query) *(optional)*: integer (int32) - Number of records per page
- **filter** (query) *(optional)*: string - Filter by user email
- **clientWorkloadId** (query) *(optional)*: string - Filter by client workload ID or name
- **order** (query) *(optional)*: string - Order by field and direction (e.g. 'creationTime desc')

**Responses:**

- **200**: Page of user sessions
  - `application/json`: [SsoIdpUserSessionDTOListDTO](../schemas/sso-idp-user-session-dto-list-dto.md)
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
  "https://your-tenant.aembit.io/api/v1/sso-idps/{id}/user-sessions?page=example-value&per-page=example-value&filter=example-value&clientWorkloadId=example-value&order=example-value"
```
