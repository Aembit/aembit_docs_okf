* [ApiCredentialsRequest](api-credentials-request.md) - Request payload for retrieving credentials for a Client Workload
* [ApiCredentialsResponse](api-credentials-response.md) - Response containing credentials that a Client Workload requests with expiration details
* [AuthRequest](auth-request.md) - Identity and attestation information for Client Workload authentication. 
* [AwsDTO](aws-dto.md) - AWS-specific attestation data for Client Workload identification
* [AwsEcsDTO](aws-ecs-dto.md) - AWS ECS container and task metadata for workload attestation
* [AzureAttestationDTO](azure-attestation-dto.md) - Azure-specific attestation data for Client Workload identification
* [AzureAttestedDocumentDTO](azure-attested-document-dto.md) - Azure Instance Metadata Service (IMDS) Attested Data document.
* [ClientWorkloadDetails](client-workload-details.md) - Identity and attestation information for a Client Workload requesting credentials
* [ConnectionMetadata](connection-metadata.md) - Filter for multi-credential provider access policy credential request
* [CredentialProviderTypes](credential-provider-types.md) - Type of credential being requested from your configured Credential Provider.
* [CrowdStrikeDTO](crowd-strike-dto.md) - CrowdStrike agent information for endpoint security attestation
* [EdgeCredentials](edge-credentials.md) -     Credential data returned to Client Workloads based on your configured Credential Providers
* [EnvironmentDTO](environment-dto.md) - Environment variables available to the Client Workload
* [GcpAttestationDTO](gcp-attestation-dto.md) - GCP-specific attestation data for Client Workload identification
* [GenericResponseDTO](generic-response-dto.md) - DTO for a Generic API Response
* [HostDTO](host-dto.md) - Host system information for Client Workload attestation
* [IdentityTokenAttestationDTO](identity-token-attestation-dto.md) - JWT-based identity token attestation for CI/CD platforms
* [K8sDTO](k8s-dto.md) - Kubernetes-specific attestation data for Kubernetes pod identification
* [LambdaDTO](lambda-dto.md) - AWS Lambda function information for serverless workload attestation
* [NetworkInterfacesDTO](network-interfaces-dto.md)
* [OsDTO](os-dto.md) - Operating system environment information for Client Workload attestation
* [ProcessDTO](process-dto.md) - Process information for Client Workload identification
* [SensorsDTO](sensors-dto.md) - Security sensor data for enhanced Client Workload attestation
* [ServerWorkloadDetails](server-workload-details.md) - Target resource details for which the credential is being requested. These fields are used to match the request against your configured Access Policies.
* [StsGetCallerIdentityDTO](sts-get-caller-identity-dto.md) - AWS STS GetCallerIdentity request data for identity verification
* [TokenDTO](token-dto.md) - OAuth2-style access token response with expiration details
* [TransportProtocol](transport-protocol.md)
