---
type: reference
title: "GET /api/v1/health"
description: "Aembit Cloud API Health"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["health"]
timestamp: 2026-07-28T14:42:14-04:00
---

# GET /api/v1/health

Get the health of the Aembit Cloud API.

**Operation ID:** `get-health`

**Responses:**

- **200**: API Health
  - `application/json`: [HealthDTO](../schemas/health-dto.md)

**Example:**

```bash
curl -X GET \
  -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/health"
```
