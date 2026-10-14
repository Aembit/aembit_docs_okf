---
type: reference
title: "RevokeUserSessionRequestDTO"
description: "Request payload to revoke SSO Identity Provider user sessions"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-10-13T16:56:08-07:00
---

# RevokeUserSessionRequestDTO

Request payload to revoke SSO Identity Provider user sessions

**Type:** object

**Properties:**

- **grantKey** *(optional)*: null,string - Grant key for revoking a single specific session
- **idpUserId** *(optional)*: null,string - IdP user identifier (user email) for revoking all sessions for a user
- **clientWorkloadId** *(optional)*: null,string - Optional client workload ID or name filter when revoking filtered sessions
- **revokeAll** *(optional)*: null,boolean - Optional flag to revoke all active sessions for the identity provider
