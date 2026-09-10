---
type: how-to
title: "Hostname"
description: "This document describes how the Hostname method identifies Client Workloads in Aembit for Virtual Machine deployments."
resource: https://docs.aembit.io/user-guide/access-policies/client-workloads/identification/hostname/
interface: web-ui
tags: ["vm", "identification", "client-workload", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Hostname


The Hostname Client Workload identification method is applicable to Virtual Machine deployments and utilizes the hostname of the machine (which can be retrieved by the hostname command) to identify and distinguish Client Workloads.

## Applicable Deployment Type

This method is suitable for Aembit Edge-based deployments.

## Configuration

### Aembit Cloud

1. Create a new Client Workload.
2. Choose **Hostname** for client identification.
3. In the **Value** field, enter the hostname of the virtual machine where the Client Workload is running.

### Finding the Hostname

* Open a terminal on your Linux VM.
* Use the `hostname -f` command to retrieve its hostname.

Alternatively, you can often find the hostname in the Virtual Machine’s configuration settings or system information.

### Uniqueness

Ensure the hostname is unique within your organization to avoid unintentionally matching other Virtual Machines. If necessary, consider combining Hostname with other client identifiers. Please consult the [Client Workload multiple identifiers](client-workload-multiple-ids.md) documentation to enhance uniqueness.

## Related

**Compatible trust providers**

* [AWS Metadata Service](../../trust-providers/aws-metadata-service-trust-provider.md)
* [AWS Role](../../trust-providers/aws-role-trust-provider.md)
* [Azure Instance Metadata Service](../../trust-providers/azure-metadata-service-trust-provider.md)
* [Certificate Signed Attestation](../../trust-providers/certificate-signed-attestation-trust-provider.md)
* [GCP Identity Token](../../trust-providers/gcp-identity-token-trust-provider.md)
* [Kerberos](../../trust-providers/kerberos-trust-provider.md)
* [OIDC ID Token](../../trust-providers/oidc-id-token-trust-provider.md)
