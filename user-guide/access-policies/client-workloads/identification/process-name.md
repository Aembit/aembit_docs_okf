---
type: how-to
title: "Process Name"
description: "This document describes how the Process Name method identifies Client Workloads in Aembit for Virtual Machine deployments."
resource: https://docs.aembit.io/user-guide/access-policies/client-workloads/identification/process-name/
interface: web-ui
tags: [vm, identification, client-workload, access-policy]
timestamp: 2025-05-29T11:26:12-07:00
type_inferred: true
---

# Process Name

The Process Name Client Workload identification method is applicable to Virtual Machine deployments and utilizes the name of the process associated with the Client Workload to identify and distinguish it from other workloads.

## Applicable deployment type

[Section titled “Applicable deployment type”](#applicable-deployment-type)

This method is suitable for Aembit Edge-based deployments.

## Configuration

[Section titled “Configuration”](#configuration)

As of **Agent Proxy** version 1.23.3002, to use this method of client workload identification, you must set the `AEMBIT_CLIENT_WORKLOAD_PROCESS_IDENTIFICATION_ENABLED` to `true`. By default, its value is `false`.

See [Edge Component environment variables reference](../../../../reference/edge-components/edge-component-env-vars.md) for details.

### Aembit Cloud

[Section titled “Aembit Cloud”](#aembit-cloud)

1. Create a new Client Workload.
2. Choose **Process Name** for client identification.
3. In the **Value** field, enter the exact name of the process that represents the Client Workload.

### Finding the process name

[Section titled “Finding the process name”](#finding-the-process-name)

* Open a terminal on your Linux VM.
* Use system monitoring tools, or commands like `ps` or `top` on the virtual machine, to list running processes and identify the relevant process name.

Alternatively, you can often find the process name in the Client Workload’s configuration files or documentation.

### Uniqueness

[Section titled “Uniqueness”](#uniqueness)

Process name identification is inherently not unique, as processes with the same name could exist on multiple virtual machines. To enhance uniqueness, consider combining Process Name with other client identifiers, such as Hostname. For more information on using multiple identifiers effectively, see [Client Workload multiple identifiers](client-workload-multiple-ids.md) documentation to enhance uniqueness.

## Related

**Compatible trust providers**

* [AWS Metadata Service](../../trust-providers/aws-metadata-service-trust-provider.md)
* [AWS Role](../../trust-providers/aws-role-trust-provider.md)
* [Azure Instance Metadata Service](../../trust-providers/azure-metadata-service-trust-provider.md)
* [Certificate Signed Attestation](../../trust-providers/certificate-signed-attestation-trust-provider.md)
* [GCP Identity Token](../../trust-providers/gcp-identity-token-trust-provider.md)
* [Kerberos](../../trust-providers/kerberos-trust-provider.md)
* [OIDC ID Token](../../trust-providers/oidc-id-token-trust-provider.md)
