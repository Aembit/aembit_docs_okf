---
type: how-to
title: "How to set up Agent Proxy on Windows Server"
description: "How to set up Aembit Agent Proxy on Windows Server"
resource: https://docs.aembit.io/user-guide/deploy-install/virtual-machine/windows/agent-proxy-install-windows/
interface: web-ui
tags: ["windows", "virtual-machine", "deploy-install"]
timestamp: 2026-09-08T23:32:41-07:00
---

# How to set up Agent Proxy on Windows Server

Aembit provides many different deployment options you can use to deploy Aembit Edge Components in your environment. Each of these options provide similar features and functionality. The steps for each of these options, however, are specific to the deployment option you select.

This page describes the process to deploy Agent Proxy to a Windows Server virtual machine (VM).

> **Note**
>
> Aembit recommends deploying Agent Controller on a standalone virtual machine and not collocating it with Agent Proxy.
>
> Although it’s possible to deploy both components on the same virtual machine, this isn’t recommended because you could end up managing many Agent Controllers.

## Supported versions

Use the following table to make sure that Aembit supports the operating system and platform you’re deploying to your VM:

| Operating system    | Edge Component versions |
| ------------------- | ----------------------- |
| Windows Server 2019 | Agent Proxy v1.20.2559+ |
| Windows Server 2022 | Agent Proxy v1.20.2559+ |

## Install Agent Proxy

To install Agent Proxy on Windows Server, follow these steps:

1. Download the latest [Agent Proxy Release](https://releases.aembit.io/agent_proxy/index.html) using the following PowerShell command. Note that downloading directly via a browser may result in unexpected behavior.

   ```powershell
   Invoke-WebRequest -Uri <release_url> -Outfile aembit_agent_proxy_windows_amd64_<version>.msi
   ```

2. Install Agent Proxy using `msiexec`:

   Optionally, append any [Agent Proxy environment variables](#agent-proxy-environment-variables) in the following format separated by spaces: `ENV_VAR_NAME=myvalue ENV_VAR_NAME=myvalue`

   ```powershell
   msiexec /i aembit_agent_proxy_windows_amd64_<version>.msi /l*v install.log <ENV_VAR_NAME=myvalue>
   ```

3. Configure an explicit proxy on your Windows Server VM. Common methods include Group Policy Objects (GPO), Proxy Auto-Configuration (PAC) files, system-level proxy settings, and many others.

   Since HTTP proxy configurations may have specific requirements, consult your IT administrator to determine the most appropriate method for your environment.

   > **System environment variables**
   >
   > If you’re configuring an explicit proxy using [system environment variables](https://learn.microsoft.com/en-us/windows/win32/procthread/environment-variables), you must also create a corresponding `NO_PROXY` system environment variable so that Aembit routes HTTP requests from Agent Proxy correctly. Otherwise, Agent Proxy routes traffic to itself and doesn’t function as intended.
   >
   > For the required `NO_PROXY` entries, see [`NO_PROXY`](../../../../reference/edge-components/edge-component-env-vars.md#agent-proxy-environment-variables) in the Agent Proxy environment variables reference.

   > **Windows privileges**
   >
   > If you’re configuring an explicit proxy in a managed environment (like Windows Active Directory), you may need elevated administrator privileges to do so. Check with your IT administrator to make sure you have the privileges you need to configure an explicit proxy.
   >
   > Otherwise, you shouldn’t need elevated privileges when configuring an explicit proxy directly on your Client Workload.

> **Troubleshooting**
>
> If you encounter the following error during installation or while upgrading, you may have one or more malformed environment variable values:
>
> ```text
> There is a problem with this Windows Installer package.
> A program run as part of the setup did not finish as expected.
> Contact your support personnel or package vendor.
> ```
>
> Verify your environment variable values.
>
> See [Agent Proxy environment variables](../../../../reference/edge-components/edge-component-env-vars.md) for details.

> **Note**
>
> If you are running Aembit in AWS, you may use the Agent Controller Private IP DNS name as Agent Controller Host (for example, `ip-172-31-3-73.us-west-1.compute.internal`).

### Agent Proxy environment variables

For a list of all available environment variables for configuring the Agent Proxy installer, see [Agent Proxy environment variables reference](../../../../reference/edge-components/edge-component-env-vars.md#agent-proxy-environment-variables).

### Uninstall Agent Proxy

To uninstall Agent Proxy from Windows Server VMs, follow these steps:

1. As an administrator, open the Command Prompt or PowerShell.

2. Run the following command to uninstall Agent Proxy:

   ```plaintext
   msiexec /uninstall aembit_agent_proxy_windows_amd64_<version_number>.msi /l*v uninstall.log /quiet
   ```

> **Removing logs**
>
> Uninstalling Agent Proxy doesn’t remove logs. If desired, delete logs from `C:\ProgramData\Aembit\AgentProxy\Logs`.

## Access Agent Proxy logs

Agent Proxy writes logs to `C:\ProgramData\Aembit\AgentProxy\Logs\log`.

For more information about Agent Proxy log levels, see [Agent Proxy log level reference](../../../../reference/edge-components/agent-log-level-reference.md#agent-proxy-log-levels)

## Optional configurations

The following sections describe optional configurations you can use to customize your Agent Proxy installation:

### Configuring AWS RDS certificates

To install all the possible CA Certificates for AWS Relational Database Service (RDS) databases, see [AWS RDS Certificates](../../databases/aws-rds.md).

### Configuring TLS Decrypt

To use TLS decryption on your virtual machine, download the Aembit CA certificate and add it to your trusted CAs. See [About TLS Decrypt](../../advanced-options/tls-decrypt/overview.md) for detailed instructions on how to use and configure TLS decryption on your virtual machine.

### Resource Set deployment

If you want to deploy a Resource Set using the Agent Proxy Virtual Machine Installer, you need to specify the `AEMBIT_RESOURCE_SET_ID` environment variable during the Agent Proxy installation. See [Edge Component environment variables reference](../../../../reference/edge-components/edge-component-env-vars.md) for details.

This configuration enables the Agent Proxy to support Client Workloads in this Resource Set. For more info, see [Resource Sets overview](../../../administration/resource-sets/overview.md).

### Custom environment variables for dynamic claims

To make custom environment variables available to [OIDC and JWT-SVID dynamic claims](../../../access-policies/credential-providers/advanced-options/dynamic-claims-oidc.md), set them as machine-level environment variables. For example, you can use PowerShell’s `[Environment]::SetEnvironmentVariable(..., 'Machine')` method. You must also list each variable name in [`AEMBIT_ENV_VAR_ALLOWLIST`](../../../../reference/edge-components/edge-component-env-vars.md#aembit_env_var_allowlist). Agent Proxy MSI installer accepts `AEMBIT_ENV_VAR_ALLOWLIST` as an MSI property at install time.

For step-by-step instructions, see [Configure custom environment variables for Agent Proxy](../../advanced-options/agent-proxy/configure-custom-env-vars.md#configure-by-deployment).

### Configuring gRPC keep-alives

Agent Proxy can send gRPC keep-alive messages on its connection to your Aembit Tenant. Enable them at install time for restrictive networks, such as a Secure Web Gateway that drops idle connections without closing them. Set both `AEMBIT_TENANT_GRPC_PING_INTERVAL_SECS` and `AEMBIT_TENANT_GRPC_PING_TIMEOUT_SECS` as MSI properties:

```powershell
msiexec /i aembit_agent_proxy_windows_amd64_<version>.msi /l*v install.log AEMBIT_TENANT_GRPC_PING_INTERVAL_SECS=30 AEMBIT_TENANT_GRPC_PING_TIMEOUT_SECS=10
```

These settings are off by default. The Windows installer applies these properties from Agent Proxy 1.34.5755 on. For details, see [Agent Proxy environment variables reference](../../../../reference/edge-components/edge-component-env-vars.md#agent-proxy-environment-variables).
