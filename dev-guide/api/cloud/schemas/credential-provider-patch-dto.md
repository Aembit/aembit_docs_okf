---
type: reference
title: "CredentialProviderPatchDTO"
description: "Patch request for an individual Credential Provider"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-07-28T14:42:14-04:00
---

# CredentialProviderPatchDTO

Patch request for an individual Credential Provider

**Type:** object

**Properties:**

- **name** *(optional)*: null,string - New Name for the identified entity
- **description** *(optional)*: null,string - New Description for the identified entity
- **isActive** *(optional)*: null,boolean (boolean) - New Status for the identified entity
- **tags** *(optional)*: null,array - New Tags for the identified entity
- **providerDetailJSON** *(optional)*: null,string - JSON representation of the Credential Provider configuration details
- **type** *(optional)*: null,string - Credential Provider Type (e.g. oauth-client-credential, username-password, etc.)
