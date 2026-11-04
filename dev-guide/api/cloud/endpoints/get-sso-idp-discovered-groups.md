---
type: reference
title: "GET /api/v1/sso-idps/{id}/discovered-groups"
description: "Get Discovered Groups for an SSO Identity Provider"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["sso-identity-provider"]
timestamp: 2026-11-03T18:06:18-08:00
---

# GET /api/v1/sso-idps/{id}/discovered-groups

Get Discovered Groups for an SSO Identity Provider

**Operation ID:** `get-sso-idp-discovered-groups`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of SSO Identity Provider
- **search** (query) *(optional)*: string - Optional prefix search string
- **limit** (query) *(optional)*: integer (int32) - Maximum number of groups to return

**Responses:**

- **200**: Discovered groups for the identity provider
  - `application/json`: [DiscoveredGroupListDTO](../schemas/discovered-group-list-dto.md)
- **404**: SSO Identity Provider not found
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/sso-idps/{id}/discovered-groups?search=example-value&limit=example-value"
```
