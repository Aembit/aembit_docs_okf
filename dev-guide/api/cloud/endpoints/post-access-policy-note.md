---
type: reference
title: "POST /api/v1/access-policies/{id}/notes"
description: "Add a note to an Access Policy"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["access-policy-deprecated"]
timestamp: 2026-09-22T20:22:25-07:00
---

# POST /api/v1/access-policies/{id}/notes

Add a note to an Access Policy.

**Operation ID:** `post-access-policy-note`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Access Policy
- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Request Body:**

PolicyNoteDTO

- `application/json`: [PolicyNoteDTO](../schemas/policy-note-dto.md)

**Responses:**

- **201**: Note added to an Access Policy
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X POST \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/access-policies/{id}/notes"
```
