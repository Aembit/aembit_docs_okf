---
type: reference
title: "AuditActorDTO"
description: "DTO for the Actor details of an Aembit Audit Log"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
timestamp: 2025-08-28T08:38:37-04:00
---

# AuditActorDTO

DTO for the Actor details of an Aembit Audit Log

**Type:** object

**Properties:**

- **type** *(optional)*: string | null - The type of Audit Log actor (e.g. User, System, or Role)
- **displayName** *(optional)*: string | null - Fully qualified Audit Log Actor name
- **userName** *(optional)*: string | null
- **email** *(optional)*: string | null
- **credentialProviderId** *(optional)*: string | null - Credential Provider ID that was used to generate the Role-based Access Token for this Audit Log action
- **accessPolicyId** *(optional)*: string | null - Access Policy ID that was used to generate the Role-based Access Token for this Audit Log action
