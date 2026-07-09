---
type: reference
title: "AuthorizationEventDataMetaDTO"
description: "Metadata DTO for an individual Aembit Access Authorization Event"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
timestamp: 2025-08-28T08:38:37-04:00
---

# AuthorizationEventDataMetaDTO

Metadata DTO for an individual Aembit Access Authorization Event

**Type:** object

**Properties:**

- **clientIP** *(optional)*: string | null - Remote Client IP Address of the Access Authorization Request
- **timestamp** *(optional)*: string (date-time) - Timestamp of the Access Authorization Request
- **eventType** *(optional)*: string | null - Event Type of the Access Authorization Request
- **eventId** *(optional)*: string (uuid) - Unique ID of the Access Authorization Event
- **resourceSetId** *(optional)*: string (uuid) - Resource Set ID of the Access Authorization Event
- **contextId** *(optional)*: string (uuid) - Context ID of the Access Authorization Events for a single Access Authorization Request
- **directiveId** *(optional)*: string (uuid) - Directive ID of the Access Authorization Event (if available)
- **severity** *(optional)*: string | null - Severity of the Access Authorization Event (e.g. Info, Warning, Error)
