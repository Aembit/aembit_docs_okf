---
type: reference
title: "PATCH /api/v2/integrations/{id}"
description: "Patch an Integration"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["integration-v2"]
timestamp: 2026-09-22T20:22:25-07:00
---

# PATCH /api/v2/integrations/{id}

Patch an Integration as identified by its ID.

**Operation ID:** `patch-integration2`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Integration
- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Request Body:**

IntegrationPatchDTO

- `application/json`: [IntegrationPatchDTO](../schemas/integration-patch-dto.md)

**Responses:**

- **200**: Patched Integration
  - `application/json`: any
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X PATCH \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v2/integrations/{id}"
```
