---
type: reference
title: "AuditActorDTO"
description: "DTO for the Actor details of an Aembit Audit Log"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-07-28T14:42:14-04:00
---

# AuditActorDTO

DTO for the Actor details of an Aembit Audit Log

**Type:** object

**Properties:**

- **type** *(optional)*: null,string - The type of Audit Log actor (e.g. User, System, or Role)
- **displayName** *(optional)*: null,string - Fully qualified Audit Log Actor name
- **userName** *(optional)*: null,string
- **email** *(optional)*: null,string
- **credentialProviderId** *(optional)*: null,string - Credential Provider ID that was used to generate the Role-based Access Token for this Audit Log action
- **accessPolicyId** *(optional)*: null,string - Access Policy ID that was used to generate the Role-based Access Token for this Audit Log action
