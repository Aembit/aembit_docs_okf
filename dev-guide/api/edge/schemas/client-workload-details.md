---
type: reference
title: "ClientWorkloadDetails"
description: "Identity and attestation information for a Client Workload. \r"
resource: https://docs.aembit.io/dev-guide/api/edge/api-reference-edge/
interface: api
timestamp: 2026-09-22T20:31:55-07:00
---

# ClientWorkloadDetails

Identity and attestation information for a Client Workload. 
When requesting credentials via /edge/v1/credentials, this object must be populated 
with the same workload identity context and platform-specific attestation data 
(e.g., Kubernetes service account token, OIDC identity token, or cloud instance 
attestation data) used during the initial /edge/v1/auth request. 
Aembit re-evaluates these details against configured access policies for every 
request to ensure continuous compliance with trust requirements.

**Type:** object

**Properties:**

- **sourceIP** *(optional)*: null,string - IP address of the requesting Client Workload
- **aws** *(optional)*: [AwsDTO](aws-dto.md) - AWS-specific attestation data for Client Workload identification
- **azure** *(optional)*: [AzureAttestationDTO](azure-attestation-dto.md) - Azure-specific attestation data for Client Workload identification
- **gcp** *(optional)*: [GcpAttestationDTO](gcp-attestation-dto.md) - GCP-specific attestation data for Client Workload identification
- **os** *(optional)*: [OsDTO](os-dto.md) - Operating system environment information for Client Workload attestation
- **k8s** *(optional)*: [K8sDTO](k8s-dto.md) - Kubernetes-specific attestation data for Kubernetes pod identification
- **host** *(optional)*: [HostDTO](host-dto.md) - Host system information for Client Workload attestation
- **github** *(optional)*: [IdentityTokenAttestationDTO](identity-token-attestation-dto.md) - JWT-based identity token attestation for CI/CD platforms
- **terraform** *(optional)*: [IdentityTokenAttestationDTO](identity-token-attestation-dto.md) - JWT-based identity token attestation for CI/CD platforms
- **gitlab** *(optional)*: [IdentityTokenAttestationDTO](identity-token-attestation-dto.md) - JWT-based identity token attestation for CI/CD platforms
- **oidc** *(optional)*: [IdentityTokenAttestationDTO](identity-token-attestation-dto.md) - JWT-based identity token attestation for CI/CD platforms
- **saml** *(optional)*: [IdentityTokenAttestationDTO](identity-token-attestation-dto.md) - JWT-based identity token attestation for CI/CD platforms
