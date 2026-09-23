---
type: reference
title: "AzureAttestedDocumentDTO"
description: "Azure Instance Metadata Service (IMDS) Attested Data document."
resource: https://docs.aembit.io/dev-guide/api/edge/api-reference-edge/
interface: api
timestamp: 2026-09-22T20:31:55-07:00
---

# AzureAttestedDocumentDTO

Azure Instance Metadata Service (IMDS) Attested Data document.

**Type:** object

**Properties:**

- **encoding** *(optional)*: null,string - The encoding format of the Azure IMDS document (e.g., pkcs7).
- **signature** *(optional)*: null,string - The Base64-encoded signature (PKCS#7 container) returned by the Azure IMDS /metadata/attested/document endpoint in its signature property.
- **nonce** *(optional)*: null,string - A cryptographic nonce generated independently by the Edge API client, passed to the Azure IMDS endpoint, and forwarded to Aembit for replay protection.
