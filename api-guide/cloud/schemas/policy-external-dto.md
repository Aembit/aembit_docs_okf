---
type: reference
title: "PolicyExternalDTO"
description: "Individual Access Policy"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
timestamp: 2025-08-28T08:38:37-04:00
---

# PolicyExternalDTO

Individual Access Policy

**Type:** object

**Properties:**

- **externalId** *(optional)*: string (uuid)
- **name** *(required)*: string - Name of the Entity
- **description** *(optional)*: string | null - Description of the Entity
- **isActive** *(required)*: boolean (boolean) - True/False value that determines if this entity is Active or Disabled
- **tags** *(optional)*: Array of [TagDTO](tag-dto.md)
- **createdAt** *(optional)*: string (date-time)
- **modifiedAt** *(optional)*: string (date-time) | null
- **createdBy** *(optional)*: string | null
- **modifiedBy** *(optional)*: string | null
- **resourceSet** *(required)*: string (uuid) - ID of the Resource Set in which this Access Entity exists
- **clientWorkload** *(optional)*: [ClientWorkloadExternalDTO](client-workload-external-dto.md)
- **trustProviders** *(optional)*: Array of [TrustProviderDTO](trust-provider-dto.md) - Details of the Trust Providers associated with this Access Policy
- **accessConditions** *(optional)*: Array of [AccessConditionDTO](access-condition-dto.md) - Details of the Access Conditions associated with this Access Policy
- **credentialProvider** *(optional)*: [CredentialProviderDTO](credential-provider-dto.md)
- **serverWorkload** *(optional)*: [ServerWorkloadExternalDTO](server-workload-external-dto.md)
- **policyNotes** *(optional)*: Array of [PolicyNoteDTO](policy-note-dto.md) - Policy Notes for this Access Policy
