---
type: how-to
title: "Process User Name"
description: "How to identify workloads on Virtual Machines using the Process User Name within Aembit"
resource: https://docs.aembit.io/user-guide/access-policies/client-workloads/identification/process-user-name/
interface: web-ui
tags: [vm, identification, client-workload, access-policy]
timestamp: 2025-06-09T09:41:35-07:00
type_inferred: true
---

# Process User Name

This page explains how to use the **Process User Name** identifier to identify workloads deployed on **Virtual Machines**.

## Understanding the process user name identifier

[Section titled “Understanding the process user name identifier”](#understanding-the-process-user-name-identifier)

The Process User Name is the name of the system user under which the Client Workload process runs on a Virtual Machine.\
This can help distinguish workloads based on ownership or context when multiple processes are running on the same VM.

This method is especially useful when workloads run under unique system users or user accounts.

## Applicable deployment type

[Section titled “Applicable deployment type”](#applicable-deployment-type)

Aembit supports the Process User Name identification method for Edge-based deployments on **Linux** [Virtual Machines](../../../deploy-install/virtual-machine/overview.md).

## Create a Client Workload with a process user name identifier

[Section titled “Create a Client Workload with a process user name identifier”](#create-a-client-workload-with-a-process-user-name-identifier)

To configure a Client Workload with a Process User Name identifier, follow these steps:

1. Log into your Aembit Tenant.

2. Click **Client Workloads** in the left nav pane.

3. Click **New**, revealing the **Client Workload** pop out menu.

4. Enter the **Name** and optional **Description** for the Client Workload.

5. Under **Client Identification**, select **Process User Name**.

   For **Value**, enter the exact user name under which the workload process runs on the Virtual Machine.

   For example, if your process runs under the user `service-user`, enter `service-user` in the **Value** field.

   If you’re unsure how to find the user name, see [Find the process user name](#find-the-process-user-name).

6. Click **Save**.

   Aembit displays the new Client Workload on the **Client Workloads** page.

Client Workload identifier uniqueness

When you identify a Client Workload using a single identifier such as this one in a complex environment, this identifier may not always provide sufficient uniqueness.

To avoid Aembit unintentionally matching something beyond what it’s intended to match, Aembit recommends that you set additional identifiers to compliment this one, creating a Client Workload identity that’s unique across your environment.

See [Using multiple Client Workload identifiers](client-workload-multiple-ids.md) for guidance on combining this identifier with more specific identifiers such as those on the [Client Workload Identifiers overview](overview.md) page, which includes vendor- and technology-specific identifiers.

## Find the process user name

[Section titled “Find the process user name”](#find-the-process-user-name)

To find the user name associated with a process on a Virtual Machine, follow these steps:

Note

You can also find the process user in service definitions, application launch scripts, or infrastructure configuration.

1. Open a terminal on your Virtual Machine.

2. Use a process monitoring command, such as:

   ```shell
   ps aux | grep <your-process-name>
   ```

3. Look at the `USER` column in the output to find the user running the process.

   This is the value to use as the Process User Name in your Aembit Client Workload configuration.

## Related

**Compatible trust providers**

* [AWS Metadata Service](../../trust-providers/aws-metadata-service-trust-provider.md)
* [AWS Role](../../trust-providers/aws-role-trust-provider.md)
* [Azure Instance Metadata Service](../../trust-providers/azure-metadata-service-trust-provider.md)
* [Certificate Signed Attestation](../../trust-providers/certificate-signed-attestation-trust-provider.md)
* [GCP Identity Token](../../trust-providers/gcp-identity-token-trust-provider.md)
* [Kerberos](../../trust-providers/kerberos-trust-provider.md)
* [OIDC ID Token](../../trust-providers/oidc-id-token-trust-provider.md)
