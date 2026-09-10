---
type: reference
title: "GET /api/v1/credential-integrations/{id}"
description: "Get a Credential Provider Integration"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["credential-provider-integration"]
timestamp: 2026-07-28T14:42:14-04:00
---

# GET /api/v1/credential-integrations/{id}

Get a Credential Provider Integration identified by its ID.

**Operation ID:** `get-credential-provider-integration`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Credential Provider Integration

**Responses:**

- **200**: Credential Provider Integration
  - `application/json`: any
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/credential-integrations/{id}"
```
