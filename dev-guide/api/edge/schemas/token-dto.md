---
type: reference
title: "TokenDTO"
description: "OAuth2-style access token response with expiration details"
resource: https://docs.aembit.io/dev-guide/api/edge/api-reference-edge/
interface: api
timestamp: 2026-09-22T20:31:55-07:00
---

# TokenDTO

OAuth2-style access token response with expiration details

**Type:** object

**Properties:**

- **accessToken** *(required)*: string - Bearer token for authenticating subsequent API requests
- **tokenType** *(required)*: string - Token type, typically 'Bearer' for OAuth2-style tokens
- **expiresIn** *(required)*: integer (int32) - Token expiration time in seconds from issuance
