---
type: reference
title: "PolicyCredentialMappingDTO"
description: "Access Policy Credential Mappings"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-07-28T14:42:14-04:00
---

# PolicyCredentialMappingDTO

Access Policy Credential Mappings

**Type:** object

**Properties:**

- **credentialProviderId** *(required)*: string (uuid) - CredentialProviderId
- **mappingType** *(required)*: [PolicyCredentialProviderMappingTypes](policy-credential-provider-mapping-types.md) - Mapping Type
- **accessKeyId** *(optional)*: null,string - AWS Access Key Id
- **accountName** *(optional)*: null,string - Snowflake Username
- **headerName** *(optional)*: null,string - Header Name
- **headerValue** *(optional)*: null,string - Header Value
- **httpbodyFieldPath** *(optional)*: null,string - HttpBody Field Path
- **httpbodyFieldValue** *(optional)*: null,string - HttpBody Field Value
