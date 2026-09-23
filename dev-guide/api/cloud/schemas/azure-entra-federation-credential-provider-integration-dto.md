---
type: reference
title: "AzureEntraFederationCredentialProviderIntegrationDTO"
description: "Individual Credential Provider Integration"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-09-22T20:22:25-07:00
---

# AzureEntraFederationCredentialProviderIntegrationDTO

**Extends:** [CredentialProviderIntegrationDTO](credential-provider-integration-dto.md)

Individual Credential Provider Integration

**Type:** object

**Properties:**

- **type** *(required)*: [CredentialProviderIntegrationType](credential-provider-integration-type.md)
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
- **tokenExpiration** *(optional)*: null,string (date-time)
- **lastOperationTimestamp** *(optional)*: null,string (date-time)
- **status** *(optional)*: null,string
- **errorMessage** *(optional)*: null,string
- **audience** *(required)*: string - Audience of the federated OIDC token to authenticate against Azure Entra ID
- **subject** *(required)*: string - Subject of the federated OIDC token to authenticate against Azure Entra ID
- **azureTenant** *(required)*: string - Tenant ID of the Azure Entra ID to authenticate against Azure Key Vault
- **clientId** *(required)*: string - Client ID of the Azure application to authenticate against Azure Key Vault
- **keyVaultName** *(required)*: string - Name of the Azure Key Vault to fetch secrets from
- **fetchSecretNames** *(optional)*: boolean - Indicates whether to fetch and populate the names of secrets on Azure Key Vault Value Credential Provider UI
