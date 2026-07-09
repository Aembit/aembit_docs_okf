---
type: reference
title: "GET /api/alpha/server-workload-drafts/{id}"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [discovery-server-workload-draft]
timestamp: 2025-08-28T08:38:37-04:00
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
