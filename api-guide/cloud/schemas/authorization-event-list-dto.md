---
type: reference
title: "AuthorizationEventListDTO"
description: "Page of Aembit Access Authorization Events"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
timestamp: 2025-08-28T08:38:37-04:00
---

# AuthorizationEventListDTO

Page of Aembit Access Authorization Events

**Type:** object

**Properties:**

- **page** *(optional)*: integer (int32) - Page of entities
- **perPage** *(optional)*: integer (int32) - Number of entities requested for the current page
- **order** *(optional)*: string | null - Ordering criteria used for the current page
- **statusCode** *(optional)*: integer (int32) - HTTP Status Code of the response
- **recordsTotal** *(optional)*: integer (int32) - Total number of Aembit Audit Logs
- **authorizationEvents** *(optional)*: Array of [AuthorizationEventDTO](authorization-event-dto.md) - Page of Aembit Access Authorization Events
