---
type: reference
title: "DELETE /api/v1/credential-integrations/{id}"
description: "Delete a Credential Provider Integration"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["credential-provider-integration"]
timestamp: 2026-09-22T20:22:25-07:00
---

# DELETE /api/v1/credential-integrations/{id}

Delete a Credential Provider Integration identified by its ID.

**Operation ID:** `delete-credential-provider-integration`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Credential Provider Integration

**Responses:**

- **204**: Successfully deleted Credential Provider Integration
- **400**: Bad Request
- **401**: Not Authenticated
- **404**: Not Found
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X DELETE \
  -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/credential-integrations/{id}"
```
