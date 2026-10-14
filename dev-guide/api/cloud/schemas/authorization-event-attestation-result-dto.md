---
type: reference
title: "AuthorizationEventAttestationResultDTO"
description: "Individual Access Entity Attestation Result of an Aembit Access Authorization Event"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-10-13T16:56:08-07:00
---

# AuthorizationEventAttestationResultDTO

Individual Access Entity Attestation Result of an Aembit Access Authorization Event

**Type:** object

**Properties:**

- **id** *(optional)*: string (uuid) - Access Entity ID
- **name** *(optional)*: null,string - Access Entity Name
- **result** *(optional)*: null,string - Access Entity processing Result for this Access Authorization Event
- **reason** *(optional)*: null,string - Diagnostic failure or evaluation reason
- **attribute** *(optional)*: null,string - Evaluated attribute name on match failure
- **expectedValue** *(optional)*: null,string - Expected value for the evaluated attribute
- **actualValue** *(optional)*: null,string - Sanitized actual value received for the evaluated attribute
- **matches** *(optional)*: null,array - List of matched Access Entity Identifiers
- **expectedValues** *(optional)*: null,array - Expected values for the evaluated attribute
