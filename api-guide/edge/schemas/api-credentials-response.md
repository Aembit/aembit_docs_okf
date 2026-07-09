---
type: reference
title: "ApiCredentialsResponse"
description: "Response containing credentials that a Client Workload requests with expiration details"
resource: https://docs.aembit.io/api-guide/edge/api-reference-edge/
interface: api
timestamp: 2026-05-07T11:06:15-07:00
---

# ApiCredentialsResponse

Response containing credentials that a Client Workload requests with expiration details

**Type:** object

**Properties:**

- **credentialType** *(optional)*: [CredentialProviderTypes](credential-provider-types.md) - Type of credential returned by your configured Credential Provider
- **expiresAt** *(optional)*: null,string (date-time) - Token expiration time in ISO 8601 format, null for non-expiring credentials
- **data** *(optional)*: [EdgeCredentials](edge-credentials.md) - Credential data returned by your configured Credential Provider
