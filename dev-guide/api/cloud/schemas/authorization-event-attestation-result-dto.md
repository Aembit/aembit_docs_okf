---
type: reference
title: "AuthorizationEventAttestationResultDTO"
description: "Individual Access Entity Attestation Result of an Aembit Access Authorization Event"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-09-22T20:22:25-07:00
---

# AuthorizationEventAttestationResultDTO

Individual Access Entity Attestation Result of an Aembit Access Authorization Event

**Type:** object

**Properties:**

- **id** *(optional)*: string (uuid) - Access Entity ID
- **name** *(optional)*: null,string - Access Entity Name
- **result** *(optional)*: null,string - Access Entity processing Result for this Access Authorization Event
- **matches** *(optional)*: null,array - List of matched Access Entity Identifiers
- **reason** *(optional)*: null,string
- **attribute** *(optional)*: null,string
- **expectedValue** *(optional)*: null,string
- **expectedValues** *(optional)*: null,array
- **actualValue** *(optional)*: null,string
