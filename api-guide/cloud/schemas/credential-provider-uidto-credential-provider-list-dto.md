---
type: reference
title: "CredentialProviderUIDTOCredentialProviderListDTO"
description: "Page of Credential Providers"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
timestamp: 2025-08-28T08:38:37-04:00
---

# CredentialProviderUIDTOCredentialProviderListDTO

Page of Credential Providers

**Type:** object

**Properties:**

- **page** *(optional)*: integer (int32) - Page of entities
- **perPage** *(optional)*: integer (int32) - Number of entities requested for the current page
- **order** *(optional)*: string | null - Ordering criteria used for the current page
- **statusCode** *(optional)*: integer (int32) - HTTP Status Code of the response
- **recordsTotal** *(optional)*: integer (int32) - Total number of Credential Providers
- **credentialProviders** *(optional)*: Array of [CredentialProviderUIDTO](credential-provider-uidto.md) - Page of Credential Providers
