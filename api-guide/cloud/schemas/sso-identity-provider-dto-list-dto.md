---
type: reference
title: "SSOIdentityProviderDTOListDTO"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
timestamp: 2025-08-28T08:38:37-04:00
---

# SSOIdentityProviderDTOListDTO

**Type:** object

**Properties:**

- **page** *(optional)*: integer (int32) - Current page number of entities
- **perPage** *(optional)*: integer (int32) - Number of entities requested for the current page
- **order** *(optional)*: string | null - Ordering criteria used for the current page
- **statusCode** *(optional)*: integer (int32) - HTTP StatusCode for the current result
- **recordsTotal** *(optional)*: integer (int32) - Total number of entities available
- **entities** *(optional)*: Array of [SSOIdentityProviderDTO](sso-identity-provider-dto.md) - Page of entities for this request
