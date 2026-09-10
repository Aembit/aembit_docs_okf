---
type: reference
title: "GET /api/v1/credential-integrations/list/{type}"
description: "Get a list of Credential Provider Integrations by type"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["credential-provider-integration"]
timestamp: 2026-07-28T14:42:14-04:00
---

# GET /api/v1/credential-integrations/list/{type}

Get a list of Credential Provider Integrations by type. The most common usage is to populate a dropdown or selection list in the UI.

**Operation ID:** `get-credential-provider-integration-list`

**Parameters:**

- **type** (path) *(required)*: [CredentialProviderIntegrationType](../schemas/credential-provider-integration-type.md) - Type of Credential Provider Integration

**Responses:**

- **200**: List of Credential Provider Integrations
  - `application/json`: Array of [GuidStringKeyValuePairDto](../schemas/guid-string-key-value-pair-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/credential-integrations/list/{type}"
```
