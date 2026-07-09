---
type: reference
title: "PublicKeyValidationDTO"
description: "Response to a request for Public Key Validation"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
timestamp: 2025-08-28T08:38:37-04:00
---

# PublicKeyValidationDTO

Response to a request for Public Key Validation

**Type:** object

**Properties:**

- **isValidContent** *(optional)*: boolean - True if the Public Key was valid, False otherwise
- **thumbprint** *(optional)*: string | null - Thumbprint of the Public Key
- **expirationDate** *(optional)*: string | null - Expiration of the Public Key Certificate
- **certificateSubject** *(optional)*: string | null - Subject of the Public Key Certificate
- **message** *(optional)*: string | null - Message describing why the Public Key was not valid if IsValidContent is False
