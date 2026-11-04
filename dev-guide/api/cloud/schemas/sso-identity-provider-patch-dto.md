---
type: reference
title: "SSOIdentityProviderPatchDTO"
description: "Patch request for an individual SSO Identity Provider"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-11-03T18:06:18-08:00
---

# SSOIdentityProviderPatchDTO

Patch request for an individual SSO Identity Provider

**Type:** object

**Properties:**

- **name** *(optional)*: null,string - New Name for the identified entity
- **description** *(optional)*: null,string - New Description for the identified entity
- **isActive** *(optional)*: null,boolean (boolean) - New Status for the identified entity
- **tags** *(optional)*: null,array - New Tags for the identified entity
- **enableUserinfoGroupLookup** *(optional)*: null,boolean - Whether UserInfo group lookup is enabled for this identity provider
- **groupClaimAttribute** *(optional)*: null,string - Claim attribute name for extracting group memberships
- **groupCacheTtlMinutes** *(optional)*: null,integer (int32) - Group cache TTL in minutes
