---
type: reference
title: "PolicyExternalDTO"
description: "Individual Access Policy"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-09-22T20:22:25-07:00
---

# PolicyExternalDTO

Individual Access Policy

**Type:** object

**Properties:**

- **externalId** *(optional)*: string (uuid)
- **name** *(required)*: string - Name of the Entity
- **description** *(optional)*: null,string - Description of the Entity
- **isActive** *(required)*: boolean (boolean) - True/False value that determines if this entity is Active or Disabled
- **tags** *(optional)*: null,array
- **createdAt** *(optional)*: string (date-time)
- **modifiedAt** *(optional)*: null,string (date-time)
- **createdBy** *(optional)*: null,string
- **modifiedBy** *(optional)*: null,string
- **resourceSet** *(required)*: string (uuid) - ID of the Resource Set in which this Access Entity exists
- **clientWorkload** *(optional)*: [ClientWorkloadExternalDTO](client-workload-external-dto.md) - Details of the Client Workload associated with this Access Policy
- **trustProviders** *(optional)*: null,array - Details of the Trust Providers associated with this Access Policy
- **accessConditions** *(optional)*: null,array - Details of the Access Conditions associated with this Access Policy
- **contentSecurity** *(optional)*: null,array - Details of the Content Security associated with this Access Policy
- **credentialProvider** *(optional)*: [CredentialProviderDTO](credential-provider-dto.md) - Credential Provider associated with this Access Policy
- **serverWorkload** *(optional)*: [ServerWorkloadExternalDTO](server-workload-external-dto.md) - Details of the Server Workload associated with this Access Policy
- **policyNotes** *(optional)*: null,array - Policy Notes for this Access Policy
