---
type: reference
title: "TrustProviderDTO"
description: "Individual Trust Provider"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-09-22T20:22:25-07:00
---

# TrustProviderDTO

Individual Trust Provider

**Type:** object

**Properties:**

- **externalId** *(optional)*: string (uuid)
- **name** *(required)*: string - Name of the Entity
- **description** *(optional)*: null,string - Description of the Entity
- **isActive** *(required)*: boolean (boolean) - True/False value that determines if this entity is Active or Disabled
- **tags** *(optional)*: null,array
- **createdAt** *(optional)*: string (date-time)
- **modifiedAt** *(optional)*: null,string (date-time)
- **createdBy** *(optional)*: null,string
- **modifiedBy** *(optional)*: null,string
- **resourceSet** *(required)*: string (uuid) - ID of the Resource Set in which this Access Entity exists
- **id** *(optional)*: integer (int32) - Trust Provider Id
- **provider** *(required)*: string - Trust Provider Type. Valid values: AWSMetadataService, KubernetesServiceAccount, AzureKubernetesService, AmazonElasticKubernetesService, GoogleKubernetesEngine, AzureMetadataService, GcpIdentityToken, Kerberos, GitHubIdentityToken, TerraformIdentityToken, GitLabIdentityToken, AWSRole, OidcIdToken, CertificateSignedAttestation, Samlv2Response, UserIdentityProvider, AWSAlbJwt, GcpIapJwt.
- **matchRules** *(optional)*: null,array - Trust Provider Match Rules
- **certificate** *(optional)*: null,string - Trust Provider Certificate or Public Key for cryptographic attestation
- **jwks** *(optional)*: null,string - Jwks Content for cryptographic attestation
- **publicKeyValidation** *(optional)*: [PublicKeyValidationDTO](public-key-validation-dto.md) - Response to a request for Public Key Validation
- **oidcUrl** *(optional)*: null,string - OIDC URL to use for retrieving JWKS Public Keys
- **oidcUrls** *(optional)*: null,array - OIDC URLs to use for retrieving JWKS Public Keys
- **pemType** *(optional)*: null,string - PEM Input Type
- **accessPolicyCount** *(optional)*: integer (int32) - Access Policies associated with this Trust Provider
- **agentControllersCount** *(optional)*: integer (int32) - Agent Controllers associated with this Trust Provider
- **agentControllerIds** *(optional)*: null,array - Agent Controller IDs associated with this Trust Provider
- **isAembitTenantOidcToken** *(optional)*: boolean
- **metadataUrl** *(optional)*: null,string - Metadata URL of the remote SSO Identity Provider
- **metadataXml** *(optional)*: null,string - Metadata XML content of the remote SSO Identity Provider
