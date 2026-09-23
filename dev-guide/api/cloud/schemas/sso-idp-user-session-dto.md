---
type: reference
title: "SsoIdpUserSessionDTO"
description: "SSO Identity Provider User Session"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-09-22T20:22:25-07:00
---

# SsoIdpUserSessionDTO

SSO Identity Provider User Session

**Type:** object

**Properties:**

- **key** *(optional)*: null,string - Grant key identifying the session
- **subjectId** *(optional)*: null,string - User email or subject identifier
- **userEmail** *(optional)*: null,string - User email extracted from idp_user_id claim
- **clientWorkloadId** *(optional)*: null,string - Client workload ID extracted from client_workload_id claim
- **clientWorkloadName** *(optional)*: null,string - Client workload name associated with the session
- **creationTime** *(optional)*: string (date-time) - Session creation timestamp
- **expiration** *(optional)*: null,string (date-time) - Session expiration timestamp
- **status** *(optional)*: null,string - Session status (Active or Revoked)
- **lifetimeFormatted** *(optional)*: null,string - Human readable session lifetime
