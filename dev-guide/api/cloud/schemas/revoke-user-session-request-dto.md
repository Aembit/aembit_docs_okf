---
type: reference
title: "RevokeUserSessionRequestDTO"
description: "Request payload to revoke SSO Identity Provider user sessions"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-09-25T10:20:24-07:00
---

# RevokeUserSessionRequestDTO

Request payload to revoke SSO Identity Provider user sessions

**Type:** object

**Properties:**

- **grantKey** *(optional)*: null,string - Grant key for revoking a single specific session
- **subjectId** *(optional)*: null,string - Subject identifier (user email) for revoking all sessions for a user
- **clientId** *(optional)*: null,string - Optional client ID filter when revoking by subject
- **revokeAll** *(optional)*: null,boolean - Optional flag to revoke all active sessions for the identity provider
