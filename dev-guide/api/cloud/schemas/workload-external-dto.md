---
type: reference
title: "WorkloadExternalDTO"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-07-28T14:42:14-04:00
---

# WorkloadExternalDTO

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
- **trustProviders** *(optional)*: null,array
- **credentialProviderId** *(optional)*: null,string (uuid)
- **credentialProviderText** *(optional)*: null,string
- **workloadServiceEndpoints** *(optional)*: null,array
- **serviceEndpoint** *(optional)*: [WorkloadServiceEndpointDTO](workload-service-endpoint-dto.md) - Service Endpoint for a Server Workload
- **type** *(optional)*: null,string
