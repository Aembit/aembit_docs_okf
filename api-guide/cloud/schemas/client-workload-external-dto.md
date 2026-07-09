---
type: reference
title: "ClientWorkloadExternalDTO"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
timestamp: 2025-08-28T08:38:37-04:00
---

# ClientWorkloadExternalDTO

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
- **identities** *(optional)*: Array of [ClientWorkloadIdentityDTO](client-workload-identity-dto.md)
- **standaloneCertificateAuthority** *(optional)*: string (uuid) | null - Standalone Certificate Authority associated with this Client Workload
- **type** *(optional)*: string | null
- **accessPolicyCount** *(optional)*: integer (int32) - Access Policies associated with this Client Workload
