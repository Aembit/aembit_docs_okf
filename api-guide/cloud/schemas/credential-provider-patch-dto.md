---
type: reference
title: "CredentialProviderPatchDTO"
description: "Patch request for an individual Credential Provider"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
timestamp: 2025-08-28T08:38:37-04:00
---

# CredentialProviderPatchDTO

Patch request for an individual Credential Provider

**Type:** object

**Properties:**

- **name** *(optional)*: string | null - New Name for the identified entity
- **description** *(optional)*: string | null - New Description for the identified entity
- **isActive** *(optional)*: boolean (boolean) | null - New Status for the identified entity
- **tags** *(optional)*: Array of [TagDTO](tag-dto.md) - New Tags for the identified entity
- **providerDetailJSON** *(optional)*: string | null - JSON representation of the Credential Provider configuration details
- **type** *(optional)*: string | null - Credential Provider Type (e.g. oauth-client-credential, username-password, etc.)
