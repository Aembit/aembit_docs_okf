---
type: reference
title: "StandaloneCertificateResponseDTO"
description: "Individual Standalone Certificate Authority"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-07-28T14:42:14-04:00
---

# StandaloneCertificateResponseDTO

Individual Standalone Certificate Authority

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
- **leafLifetime** *(required)*: integer (int32) - Leaf certificate lifetime value for this Standalone Certificate Authority
- **notBefore** *(optional)*: string (date-time) - Not before value of the Root CA for this Standalone Certificate Authority 
- **notAfter** *(optional)*: string (date-time) - Not after value of the Root CA for this Standalone Certificate Authority
- **clientWorkloadCount** *(optional)*: null,integer (int32) - Client Workloads associated with this Standalone Certificate Authority
