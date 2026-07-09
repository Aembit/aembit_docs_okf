---
type: reference
title: "PolicyCredentialMappingDTO"
description: "Access Policy Credential Mappings"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
timestamp: 2025-08-28T08:38:37-04:00
---

# PolicyCredentialMappingDTO

Access Policy Credential Mappings

**Type:** object

**Properties:**

- **credentialProviderId** *(required)*: string (uuid) - CredentialProviderId
- **mappingType** *(required)*: [PolicyCredentialProviderMappingTypes](policy-credential-provider-mapping-types.md)
- **accountName** *(optional)*: string | null - Snowflake Username
- **headerName** *(optional)*: string | null - Header Name
- **headerValue** *(optional)*: string | null - Header Value
- **httpbodyFieldPath** *(optional)*: string | null - HttpBody Field Path
- **httpbodyFieldValue** *(optional)*: string | null - HttpBody Field Value
