---
type: reference
title: "GET /api/v1/client-workloads/identifiers"
description: "Get Client Identifier List"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["client-workload"]
timestamp: 2026-07-28T14:42:14-04:00
---

# GET /api/v1/client-workloads/identifiers

Get list of client identifier objects

**Operation ID:** `get-client-identifiers`

**Responses:**

- **200**: Client Identifiers
  - `application/json`: Array of [ClientIdentifierExternalDTO](../schemas/client-identifier-external-dto.md)
- **204**: Client Identifiers Not Found
- **400**: Bad Request
- **401**: Not Authenticated

**Example:**

```bash
curl -X GET \
  -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/client-workloads/identifiers"
```
