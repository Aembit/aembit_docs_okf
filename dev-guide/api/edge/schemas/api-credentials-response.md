---
type: reference
title: "ApiCredentialsResponse"
description: "Response containing credentials that a Client Workload requests with expiration details"
resource: https://docs.aembit.io/dev-guide/api/edge/api-reference-edge/
interface: api
timestamp: 2026-09-25T10:20:14-07:00
---

# ApiCredentialsResponse

Response containing credentials that a Client Workload requests with expiration details

**Type:** object

**Properties:**

- **credentialType** *(required)*: [CredentialProviderTypes](credential-provider-types.md) - Type of credential returned by your configured Credential Provider
- **expiresAt** *(optional)*: null,string (date-time) - Token expiration time in ISO 8601 format, null for non-expiring credentials
- **data** *(required)*: [EdgeCredentials](edge-credentials.md) - Credential data returned by your configured Credential Provider
