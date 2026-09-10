---
type: reference
title: "PATCH /api/v1/credential-integrations/{id}"
description: "Patch a Credential Provider Integration"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["credential-provider-integration"]
timestamp: 2026-07-28T14:42:14-04:00
---

# PATCH /api/v1/credential-integrations/{id}

Patch a Credential Provider Integration identified by its ID.

**Operation ID:** `patch-credential-provider-integration`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Credential Provider Integration

**Request Body:**

CredentialProviderIntegrationPatchDTO

- `application/json`: [CredentialProviderIntegrationPatchDTO](../schemas/credential-provider-integration-patch-dto.md)

**Responses:**

- **200**: Patched Credential Provider Integration
  - `application/json`: any
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X PATCH \
  -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/credential-integrations/{id}"
```
