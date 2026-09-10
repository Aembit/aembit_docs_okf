---
type: reference
title: "POST /api/v1/credential-integrations"
description: "Create a Credential Provider Integration"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["credential-provider-integration"]
timestamp: 2026-07-28T14:42:14-04:00
---

# POST /api/v1/credential-integrations

Create a Credential Provider Integration.

**Operation ID:** `post-credential-provider-integration`

**Request Body:**

CredentialProviderIntegrationDTO

- `application/json`: any

**Responses:**

- **201**: Created Credential Provider Integration
  - `application/json`: any
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X POST \
  -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/credential-integrations"
```
