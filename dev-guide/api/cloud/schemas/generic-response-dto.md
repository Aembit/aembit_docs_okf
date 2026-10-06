---
type: reference
title: "GenericResponseDTO"
description: "DTO for a Generic API Response"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-09-25T10:20:24-07:00
---

# GenericResponseDTO

DTO for a Generic API Response

**Type:** object

**Properties:**

- **success** *(required)*: boolean - True if the API call was successful, False otherwise
- **message** *(required)*: string - Message to indicate why the API call failed
- **id** *(optional)*: integer (int32) - Optional internal error code or entity identifier associated with the response (defaults to 0 when not applicable)
