---
type: reference
title: "TrustProviderListDTO"
description: "Page of Trust Providers"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
timestamp: 2025-08-28T08:38:37-04:00
---

# TrustProviderListDTO

Page of Trust Providers

**Type:** object

**Properties:**

- **page** *(optional)*: integer (int32) - Page of entities
- **perPage** *(optional)*: integer (int32) - Number of entities requested for the current page
- **order** *(optional)*: string | null - Ordering criteria used for the current page
- **statusCode** *(optional)*: integer (int32) - HTTP Status Code of the response
- **recordsTotal** *(optional)*: integer (int32) - Total number of Trust Providers
- **trustProviders** *(optional)*: Array of [TrustProviderDTO](trust-provider-dto.md) - Page of Trust Providers
