---
type: reference
title: "AuthorizationEventAtttestationResultDTO"
description: "Individual Access Entity Attestation Result of an Aembit Access Authorization Event"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
timestamp: 2025-08-28T08:38:37-04:00
---

# AuthorizationEventAtttestationResultDTO

Individual Access Entity Attestation Result of an Aembit Access Authorization Event

**Type:** object

**Properties:**

- **id** *(optional)*: string (uuid) - Access Entity ID
- **name** *(optional)*: string | null - Access Entity Name
- **result** *(optional)*: string | null - Access Entity processing Result for this Access Authorization Event
- **matches** *(optional)*: Array of string - List of matched Access Entity Identifiers
- **reason** *(optional)*: string | null
- **attribute** *(optional)*: string | null
- **expectedValue** *(optional)*: string | null
- **actualValue** *(optional)*: string | null
