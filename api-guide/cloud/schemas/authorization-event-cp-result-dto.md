---
type: reference
title: "AuthorizationEventCPResultDTO"
description: "Individual Credential Provider Result of an Aembit Access Authorization Event"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
timestamp: 2025-08-28T08:38:37-04:00
---

# AuthorizationEventCPResultDTO

Individual Credential Provider Result of an Aembit Access Authorization Event

**Type:** object

**Properties:**

- **id** *(optional)*: string (uuid) - Access Entity ID
- **name** *(optional)*: string | null - Access Entity Name
- **result** *(optional)*: string | null - Access Entity processing Result for this Access Authorization Event
- **matches** *(optional)*: Array of string - List of matched Access Entity Identifiers
- **type** *(optional)*: string | null - Credential Provider Type
- **reason** *(optional)*: string | null - Credential Provider Failure Reason
