---
type: reference
title: "CrowdStrikeAIDRContentSecurityDTO"
description: "Individual Content Security"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-07-28T14:42:14-04:00
---

# CrowdStrikeAIDRContentSecurityDTO

**Extends:** [ContentSecurityDTO](content-security-dto.md)

Individual Content Security

**Type:** object

**Properties:**

- **type** *(required)*: string - Content Security Type
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
- **accessPolicyCount** *(optional)*: integer (int32) - Access Policies associated with this Content Security
- **encryptedToken** *(optional)*: null,string - The encrypted API token or client secret used to authenticate with the CrowdStrike Falcon AIDR (AI Detection and Response) service.
- **baseUrl** *(required)*: string - The base URL of the CrowdStrike Falcon AIDR service endpoint or API gateway.
- **failOpen** *(optional)*: boolean - Indicates whether requests should be allowed (fail-open) or blocked (fail-closed) if the CrowdStrike Falcon AIDR service is unreachable or encounters an error.
- **timeoutMs** *(optional)*: integer (int32) - The connection timeout in milliseconds for requests sent to the CrowdStrike Falcon AIDR service.
- **maxRetries** *(optional)*: integer (int32) - The maximum number of retry attempts for requests to the CrowdStrike Falcon AIDR service before executing the fail-open or fail-closed policy.
