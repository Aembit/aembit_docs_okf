---
type: reference
title: "TokenDTO"
description: "OAuth2-style access token response with expiration details"
resource: https://docs.aembit.io/api-guide/edge/api-reference-edge/
interface: api
timestamp: 2026-05-07T11:06:15-07:00
---

# TokenDTO

OAuth2-style access token response with expiration details

**Type:** object

**Properties:**

- **accessToken** *(required)*: string - Bearer token for authenticating subsequent API requests
- **refreshToken** *(optional)*: null,string - Refresh token to obtain new access tokens for future API authentication requests
- **tokenType** *(required)*: string - Token type, typically 'Bearer' for OAuth2-style tokens
- **expiresIn** *(required)*: integer (int32) - Token expiration time in seconds from issuance
