---
type: how-to
title: "Process Command Line"
description: "How to identify workloads on Virtual Machines using the Process Command Line within Aembit"
resource: https://docs.aembit.io/user-guide/access-policies/client-workloads/identification/process-command-line/
interface: web-ui
tags: ["vm", "identification", "client-workload", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Process Command Line

This page explains how to use the **Process Command Line** identifier to identify workloads deployed on **Virtual Machines**.

## Understanding the process command line identifier

The Process Command Line is the full command used to start a Client Workload process on a Virtual Machine, including the executable path and any arguments passed to it. This identifier is useful when multiple applications share the same process name but you can distinguish between them by their command-line arguments.

For example, if you run multiple Java applications on the same machine, you can distinguish between them using their command-line arguments:

* `java -jar /opt/apps/billing-service.jar --env production`
* `java -jar /opt/apps/analytics-service.jar --env production`

The Process Command Line identifier supports wildcard matching, which allows you to match on specific parts of the command line. For example:

* Match by executable path: `*/opt/apps/billing*`
* Match by argument: `*--env production*`
* Match by both: `*billing-service*--env production*`

Command-line arguments may contain sensitive information such as passwords, API keys, tokens, or connection strings. When you enable process identification, Aembit collects the full command line of matched processes. Make sure your application command lines don’t contain sensitive data before enabling this identifier, or refactor your applications to use environment variables or configuration files for sensitive values.

## Applicable deployment type

Aembit supports the Process Command Line identification method for Edge-based deployments on **Linux** [Virtual Machines](../../../deploy-install/virtual-machine/overview.md).

To use this method of client workload identification, you must set the `AEMBIT_CLIENT_WORKLOAD_PROCESS_IDENTIFICATION_ENABLED` environment variable to `true`. By default, its value is `false`.

See [Edge Component environment variables reference](../../../../reference/edge-components/edge-component-env-vars.md) for details.

## Create a Client Workload with a process command line identifier

To configure a Client Workload with a Process Command Line identifier, follow these steps:

1. Log into your Aembit Tenant.

2. In the sidebar, click **Client Workloads**.

3. Click **+ New** to open the Client Workload editor panel.

4. Enter the **Name** and optional **Description** for the Client Workload.

5. Under **Client Identification**, select **Process Command Line**.

   ![Client Workload editor with Process Command Line selected](https://docs.aembit.io/_astro/process-command-line-identifier.hILpNUhR_ZaKkx2.webp)

   For **Value**, enter the command line pattern that identifies the Client Workload. You can use wildcards (`*`) to match parts of the command line.

   For example:

   * To match any process running a specific JAR file: `*billing-service.jar*`
   * To match any process with a specific argument: `*--env production*`
   * To match a specific command with arguments: `/usr/bin/java*billing-service.jar*`

   If you’re unsure how to find the command line, see [Find the process command line](#find-the-process-command-line).

6. Click **Save**.

   Aembit displays the new Client Workload on the **Client Workloads** page.

> **Client Workload identifier uniqueness**
>
> When you identify a Client Workload using a single identifier such as this one in a complex environment, this identifier may not always provide sufficient uniqueness.
>
> To avoid Aembit unintentionally matching something beyond what it’s intended to match, Aembit recommends that you set additional identifiers to compliment this one, creating a Client Workload identity that’s unique across your environment.
>
> See [Using multiple Client Workload identifiers](client-workload-multiple-ids.md) for guidance on combining this identifier with more specific identifiers such as those on the [Client Workload Identifiers overview](overview.md) page, which includes vendor- and technology-specific identifiers.

## Find the process command line

To find the full command line of a process on a Virtual Machine, follow these steps:

1. Open a terminal on your Virtual Machine.

2. Find the Process Identifier (PID) of your application:

   ```shell
   ps aux | grep <your-process-name>
   ```

3. Read the full command line from the `/proc` filesystem:

   ```shell
   cat /proc/<PID>/cmdline | tr '\0' ' '
   ```

   Replace `<PID>` with the actual process ID from the previous step.

   This command returns the full command line used to start the process, including the executable path and all arguments. Use this value (or a wildcard pattern based on it) as the Process Command Line in your Aembit Client Workload configuration.

## Related

**Compatible trust providers**

* [AWS Metadata Service](../../trust-providers/aws-metadata-service-trust-provider.md)
* [AWS Role](../../trust-providers/aws-role-trust-provider.md)
* [Azure Instance Metadata Service](../../trust-providers/azure-metadata-service-trust-provider.md)
* [Certificate Signed Attestation](../../trust-providers/certificate-signed-attestation-trust-provider.md)
* [GCP Identity Token](../../trust-providers/gcp-identity-token-trust-provider.md)
* [Kerberos](../../trust-providers/kerberos-trust-provider.md)
* [OIDC ID Token](../../trust-providers/oidc-id-token-trust-provider.md)
