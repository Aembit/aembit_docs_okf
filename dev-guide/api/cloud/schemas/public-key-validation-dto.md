---
type: reference
title: "PublicKeyValidationDTO"
description: "Response to a request for Public Key Validation"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-09-22T20:22:25-07:00
---

# PublicKeyValidationDTO

Response to a request for Public Key Validation

**Type:** object

**Properties:**

- **isValidContent** *(optional)*: boolean - True if the Public Key was valid, False otherwise
- **thumbprint** *(optional)*: null,string - Thumbprint of the Public Key
- **expirationDate** *(optional)*: null,string (date-time) - Expiration of the Public Key Certificate
- **expirationDateString** *(optional)*: null,string - Expiration of the Public Key Certificate in String Format
- **certificateSubject** *(optional)*: null,string - Subject of the Public Key Certificate
- **serialNumber** *(optional)*: null,string - Serial Number of the Public Key Certificate
- **message** *(optional)*: null,string - Message describing why the Public Key was not valid if IsValidContent is False
- **pemType** *(optional)*: null,string - Certificate or Public Key
