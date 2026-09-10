---
type: reference
title: "DELETE /api/v1/certificate-authorities/{id}"
description: "Delete a Standalone Certificate Authority"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["standalone-certificate-authority"]
timestamp: 2026-07-28T14:42:14-04:00
---

# DELETE /api/v1/certificate-authorities/{id}

Delete a Standalone Certificate Authority identified by its ID.

**Operation ID:** `delete-standalone-certificate-authority`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Standalone Certificate Authority

**Responses:**

- **204**: Successfully deleted Standalone Certificate Authority
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X DELETE \
  -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/certificate-authorities/{id}"
```
