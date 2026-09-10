---
type: reference
title: "ClientWorkloadDetails"
description: "Identity and attestation information for a Client Workload requesting credentials"
resource: https://docs.aembit.io/dev-guide/api/edge/api-reference-edge/
interface: api
timestamp: 2026-09-09T13:57:06-07:00
---

# ClientWorkloadDetails

Identity and attestation information for a Client Workload requesting credentials

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
