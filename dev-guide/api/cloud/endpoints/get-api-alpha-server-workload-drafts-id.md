---
type: reference
title: "GET /api/alpha/server-workload-drafts/{id}"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["discovery-server-workload-draft"]
timestamp: 2026-09-22T20:22:25-07:00
---

# GET /api/alpha/server-workload-drafts/{id}

**Parameters:**

- **id** (path) *(required)*: string (uuid)

**Responses:**

- **200**: OK

**Example:**

```bash
curl -X GET \
  -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/alpha/server-workload-drafts/{id}"
```
