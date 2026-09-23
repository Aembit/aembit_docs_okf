---
type: reference
title: "PATCH /api/v1/discovery-integrations/{id}"
description: "Patch an Integration"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["discovery-integration"]
timestamp: 2026-09-22T20:22:25-07:00
---

# PATCH /api/v1/discovery-integrations/{id}

Patch an Integration as identified by its ID.

**Operation ID:** `patch-discovery-integration`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Integration

**Request Body:**

DiscoveryIntegrationPatchDTO

- `application/json`: [DiscoveryIntegrationPatchDTO](../schemas/discovery-integration-patch-dto.md)

**Responses:**

- **200**: Patched Integration
  - `application/json`: [DiscoveryIntegrationDTO](../schemas/discovery-integration-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X PATCH \
  -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/discovery-integrations/{id}"
```
