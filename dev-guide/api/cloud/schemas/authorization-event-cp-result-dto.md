---
type: reference
title: "AuthorizationEventCPResultDTO"
description: "Individual Credential Provider Result of an Aembit Access Authorization Event"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-07-28T14:42:14-04:00
---

# AuthorizationEventCPResultDTO

Individual Credential Provider Result of an Aembit Access Authorization Event

**Type:** object

**Properties:**

- **id** *(optional)*: string (uuid) - Access Entity ID
- **name** *(optional)*: null,string - Access Entity Name
- **result** *(optional)*: null,string - Access Entity processing Result for this Access Authorization Event
- **matches** *(optional)*: null,array - List of matched Access Entity Identifiers
- **type** *(optional)*: null,string - Credential Provider Type
- **reason** *(optional)*: null,string - Credential Provider Failure Reason
