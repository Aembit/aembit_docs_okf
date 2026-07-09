---
type: reference
title: "AzureAttestedDocumentDTO"
description: "Azure Instance Metadata Service (IMDS) Attested Data document."
resource: https://docs.aembit.io/api-guide/edge/api-reference-edge/
interface: api
timestamp: 2026-05-07T11:06:15-07:00
---

# AzureAttestedDocumentDTO

Azure Instance Metadata Service (IMDS) Attested Data document.

**Type:** object

**Properties:**

- **encoding** *(optional)*: null,string - The encoding of the IMDS document.
- **signature** *(optional)*: null,string - The Base64-encoded signature (PKCS7 container) returned by the Azure IMDS 'document' field.
- **nonce** *(optional)*: null,string - The cryptographic nonce passed to the IMDS endpoint.
