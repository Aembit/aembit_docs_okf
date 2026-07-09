---
type: reference
title: "PolicyDTO"
description: "Individual Access Policy"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
timestamp: 2025-08-28T08:38:37-04:00
---

# PolicyDTO

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
- **credentialProvider** *(optional)*: string (uuid) | null - Credential Provider associated with this Access Policy
- **trustProviders** *(optional)*: Array of string (uuid) - Trust Providers associated with this Access Policy
- **accessConditions** *(optional)*: Array of string (uuid) - Access Conditions associated with this Access Policy
- **clientWorkload** *(required)*: string (uuid) - Client Workload associated with this Access Policy
- **serverWorkload** *(required)*: string (uuid) - Server Workload associated with this Access Policy
- **clientWorkloadDetails** *(optional)*: [WorkloadExternalDTO](workload-external-dto.md)
- **serverWorkloadDetails** *(optional)*: [WorkloadExternalDTO](workload-external-dto.md)
- **policyNotes** *(optional)*: Array of [PolicyNoteDTO](policy-note-dto.md) - Policy Notes for this Access Policy
