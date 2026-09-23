---
type: reference
title: "ServerWorkloadExternalDTO"
description: "Individual Server Workload"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-09-22T20:22:25-07:00
---

# ServerWorkloadExternalDTO

Individual Server Workload

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
- **serviceEndpoint** *(required)*: [WorkloadServiceEndpointDTO](workload-service-endpoint-dto.md) - Service Endpoint of the Server Workload
- **type** *(optional)*: null,string - Type of Server Workload
- **accessPolicyCount** *(optional)*: integer (int32) - Access Policies associated with this Server Workload
