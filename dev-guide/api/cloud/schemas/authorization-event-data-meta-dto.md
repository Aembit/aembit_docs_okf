---
type: reference
title: "AuthorizationEventDataMetaDTO"
description: "Metadata DTO for an individual Aembit Access Authorization Event"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-07-28T14:42:14-04:00
---

# AuthorizationEventDataMetaDTO

Metadata DTO for an individual Aembit Access Authorization Event

**Type:** object

**Properties:**

- **clientIP** *(optional)*: null,string - Remote Client IP Address of the Access Authorization Request
- **timestamp** *(optional)*: string (date-time) - Timestamp of the Access Authorization Request
- **eventType** *(optional)*: null,string - Event Type of the Access Authorization Request
- **eventId** *(optional)*: string (uuid) - Unique ID of the Access Authorization Event
- **resourceSetId** *(optional)*: string (uuid) - Resource Set ID of the Access Authorization Event
- **contextId** *(optional)*: string (uuid) - Context ID of the Access Authorization Events for a single Access Authorization Request
- **directiveId** *(optional)*: string (uuid) - Directive ID of the Access Authorization Event (if available)
- **severity** *(optional)*: null,string - Severity of the Access Authorization Event (e.g. Info, Warning, Error)
