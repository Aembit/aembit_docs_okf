---
type: reference
title: "WorkloadExternalDTO"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
timestamp: 2025-08-28T08:38:37-04:00
---

# WorkloadExternalDTO

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
- **trustProviders** *(optional)*: Array of [TrustProviderItemDTO](trust-provider-item-dto.md)
- **credentialProviderId** *(optional)*: string (uuid) | null
- **credentialProviderText** *(optional)*: string | null
- **workloadServiceEndpoints** *(optional)*: Array of string (uuid)
- **serviceEndpoint** *(optional)*: [WorkloadServiceEndpointDTO](workload-service-endpoint-dto.md)
- **type** *(optional)*: string | null
