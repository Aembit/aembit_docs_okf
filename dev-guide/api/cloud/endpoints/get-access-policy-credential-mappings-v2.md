---
type: reference
title: "GET /api/v2/access-policies/{id}/credential-mappings"
description: "Gets a credential mappings of Access Policy"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["access-policy-v2"]
timestamp: 2026-09-22T20:22:25-07:00
---

# GET /api/v2/access-policies/{id}/credential-mappings

Retrieves credential mappings of Access Policy.

**Operation ID:** `get-access-policy-credential-mappings-v2`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Access Policy

**Responses:**

- **200**: Page of Credential Mappings
  - `application/json`: [PolicyNoteDTOListDTO](../schemas/policy-note-dto-list-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v2/access-policies/{id}/credential-mappings"
```
