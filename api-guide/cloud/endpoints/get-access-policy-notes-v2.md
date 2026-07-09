---
type: reference
title: "GET /api/v2/access-policies/{id}/notes"
description: "Gets notes for an Access Policy"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [access-policy-v2]
timestamp: 2025-08-28T08:38:37-04:00
---

# GET /api/v2/access-policies/{id}/notes

Retrieves note information for an Access Policy.

**Operation ID:** `get-access-policy-notes-v2`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Access Policy

**Responses:**

- **200**: Page of Access Policy Notes
  - `application/json`: [PolicyNoteDTOListDTO](../schemas/policy-note-dto-list-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v2/access-policies/{id}/notes"
```
