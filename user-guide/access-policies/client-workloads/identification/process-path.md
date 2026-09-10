---
type: how-to
title: "Process Path"
description: "How to identify workloads on Virtual Machines using the Process Path within Aembit"
resource: https://docs.aembit.io/user-guide/access-policies/client-workloads/identification/process-path/
interface: web-ui
tags: ["vm", "identification", "client-workload", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Process Path

This page explains how to use the **Process Path** identifier to identify workloads deployed on **Virtual Machines**.

## Understanding the process path identifier

The Process Path is the full filesystem path to the executable binary of a Client Workload process running on a Virtual Machine. This identifier is useful when multiple applications share the same process name but exist in different directories.

For example, if you run multiple Java installations on the same machine, you can distinguish between them using their paths:

* `/usr/lib/jvm/java-17-openjdk/bin/java`
* `/usr/lib/jvm/java-11-openjdk/bin/java`

## Applicable deployment type

Aembit supports the Process Path identification method for Edge-based deployments on **Linux** [Virtual Machines](../../../deploy-install/virtual-machine/overview.md).

To use this method of client workload identification, you must set the `AEMBIT_CLIENT_WORKLOAD_PROCESS_IDENTIFICATION_ENABLED` environment variable to `true`. By default, its value is `false`.

See [Edge Component environment variables reference](../../../../reference/edge-components/edge-component-env-vars.md) for details.

## Create a Client Workload with a process path identifier

To configure a Client Workload with a Process Path identifier, follow these steps:

1. Log into your Aembit Tenant.

2. In the sidebar, click **Client Workloads**.

3. Click **+ New** to open the Client Workload editor panel.

4. Enter the **Name** and optional **Description** for the Client Workload.

5. Under **Client Identification**, select **Process Path**.

   ![Client Workload editor with Process Path selected](https://docs.aembit.io/_astro/process-path-identifier.D0uwS3YL_Z1Mktkj.webp)

   For **Value**, enter the full path to the executable binary that represents the Client Workload.

   For example, if your application runs from `/opt/myapp/bin/myapp`, enter `/opt/myapp/bin/myapp` in the **Value** field.

   If you’re unsure how to find the path, see [Find the process path](#find-the-process-path).

6. Click **Save**.

   Aembit displays the new Client Workload on the **Client Workloads** page.

> **Client Workload identifier uniqueness**
>
> When you identify a Client Workload using a single identifier such as this one in a complex environment, this identifier may not always provide sufficient uniqueness.
>
> To avoid Aembit unintentionally matching something beyond what it’s intended to match, Aembit recommends that you set additional identifiers to compliment this one, creating a Client Workload identity that’s unique across your environment.
>
> See [Using multiple Client Workload identifiers](client-workload-multiple-ids.md) for guidance on combining this identifier with more specific identifiers such as those on the [Client Workload Identifiers overview](overview.md) page, which includes vendor- and technology-specific identifiers.

## Find the process path

To find the executable path of a process on a Virtual Machine, follow these steps:

> **Note**
>
> You can also find the process path in service definitions, systemd unit files, or application deployment scripts.

1. Open a terminal on your Virtual Machine.

2. Find the Process Identifier (PID) of your application:

   ```shell
   ps aux | grep <your-process-name>
   ```

3. Use `readlink` to get the full executable path:

   ```shell
   readlink -f /proc/<PID>/exe
   ```

   Replace `<PID>` with the actual process ID from the previous step.

   This command returns the full path to the executable binary. Use this value as the Process Path in your Aembit Client Workload configuration.

## Related

**Compatible trust providers**

* [AWS Metadata Service](../../trust-providers/aws-metadata-service-trust-provider.md)
* [AWS Role](../../trust-providers/aws-role-trust-provider.md)
* [Azure Instance Metadata Service](../../trust-providers/azure-metadata-service-trust-provider.md)
* [Certificate Signed Attestation](../../trust-providers/certificate-signed-attestation-trust-provider.md)
* [GCP Identity Token](../../trust-providers/gcp-identity-token-trust-provider.md)
* [Kerberos](../../trust-providers/kerberos-trust-provider.md)
* [OIDC ID Token](../../trust-providers/oidc-id-token-trust-provider.md)
