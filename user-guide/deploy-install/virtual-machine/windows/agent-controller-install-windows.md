---
type: how-to
title: "How to set up Agent Controller on Windows Server"
description: "How to set up Aembit Agent Controller on Windows Server"
resource: https://docs.aembit.io/user-guide/deploy-install/virtual-machine/windows/agent-controller-install-windows/
interface: web-ui
tags: [windows, virtual-machine, deploy-install]
timestamp: 2026-04-15T15:41:42-07:00
type_inferred: true
---

# How to set up Agent Controller on Windows Server

Aembit provides many different deployment options you can use to deploy Aembit Edge Components in your environment. Each of these options provide similar features and functionality. The steps for each of these options, however, are specific to the deployment option you select.

This page describes the process to deploy Agent Controller to a Windows Server virtual machine (VM).

Note

Aembit recommends deploying Agent Controller and Agent Proxy on standalone VMs and not collocating them on the same VM.

See [About Colocating Aembit Edge Components](../../about-colocating-edge-components.md) for more info.

To install Agent Controller on Windows Server, Aembit provides a Windows installer file (`.msi`).\
See [Installation details](#installation-details) for more information about what it does.

Aembit supports three primary configurations when you install Agent Controller on Windows Server:

* A single Windows Server.

* A single Windows Server with Kerberos attestation enabled. See [Kerberos Trust Provider](../../../access-policies/trust-providers/kerberos-trust-provider.md).

* Multiple Windows Servers in a [high availability (HA) configuration](../../advanced-options/agent-controller/agent-controller-high-availability.md) using an Active Directory [Group Managed Service Account (gMSA)](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/manage/group-managed-service-accounts/group-managed-service-accounts/group-managed-service-accounts-overview). Using a gMSA reduces the operational difficulty in managing secrets across multiple Agent Controller hosts.

## Supported versions

[Section titled “Supported versions”](#supported-versions)

Use the following table to make sure that Aembit supports the operating system and platform you’re deploying to your VM:

| Operating system    | Edge Component versions      |
| ------------------- | ---------------------------- |
| Windows Server 2019 | Agent Controller v1.21.2101+ |
| Windows Server 2022 | Agent Controller v1.21.2101+ |

## Prerequisites

[Section titled “Prerequisites”](#prerequisites)

Before you install Agent Controller on Windows Server, you must have the following:

* Network and system access to download and install software on the Windows Server host.

* If installing with Kerberos attestation enabled:

  * Your Agent Controller Windows Server host joined to an Active Directory (AD) domain.

## Install Agent Controller on Windows Server

[Section titled “Install Agent Controller on Windows Server”](#install-agent-controller-on-windows-server)

To install an Aembit Agent Controller on Windows Server:

1. Download the latest release version of the Agent Controller installer from the [Agent Controller releases page](https://releases.aembit.io/agent_controller/index.html), making sure to replace the instances of `<version>` with the latest version in the following command. Note that downloading directly via a browser may result in unexpected behavior.

   ```powershell
   Invoke-WebRequest `
      -Uri https://releases.aembit.io/agent_controller/<version>/windows/amd64/aembit_agent_controller_windows_amd64_<version>.tar.gz `
      -Outfile aembit_agent_controller.msi
   ```

   Next, follow the installation steps in the appropriate tab:

* Agent Controller

  2. Install Agent Controller using the following command. Make sure to replace `<TenantId>` with your Aembit Tenant ID and `<AgentControllerId>` with the ID of the Agent Controller you are configuring.

     ```powershell
     msiexec /i aembit_agent_controller.msi /l*v installer.log `
       AEMBIT_TENANT_ID=<TenantId> `
       AEMBIT_AGENT_CONTROLLER_ID=<AgentControllerId>
     ```

* Agent Controller + Kerberos attestation

  2. Install the Agent Controller, using the following command. Make sure to replace `<TenantId>` with your Aembit Tenant ID and `<AgentControllerId>` with the ID of the Agent Controller you are configuring.

     ```powershell
     msiexec /i aembit_agent_controller.msi /l*v installer.log `
       AEMBIT_AGENT_CONTROLLER_ID=<AgentControllerId> `
       AEMBIT_TENANT_ID=<TenantId> `
       AEMBIT_KERBEROS_ATTESTATION_ENABLED=true
     ```

  3. Make sure to add the [Kerberos Trust Provider](../../../access-policies/trust-providers/kerberos-trust-provider.md) in your Aembit Tenant.

     Caution

     When upgrading Agent Controller and you change the value of `SERVICE_LOGON_ACCOUNT`, then you must restart the Agent Controller service once installation completes.

  4. When installing the Agent Proxy, make sure the `AEMBIT_AGENT_CONTROLLER` value uses the DNS name of the Agent Controller service principal.

* Agent Controllers + Kerberos attestation + gMSA

  2. Install Agent Controller, using the following command. Run the `.msi` installer to enable Trust Provider-based Agent Controller registration, making sure to replace `<AgentControllerId>` and `<TenantId>` with the values from your Aembit Tenant.

     To install Agent Controller on Windows Server using a gMSA, you must also set the `SERVICE_LOGON_ACCOUNT` environment variable using [Down-Level Logon Name format](https://learn.microsoft.com/en-us/windows/win32/secauthn/user-name-formats#down-level-logon-name) `SERVICE_LOGON_ACCOUNT=<NetBIOS domain name>\\<sAMAccountName of gMSA>`.

     ```powershell
     msiexec /i aembit_agent_controller.msi /l*v installer.log `
       AEMBIT_AGENT_CONTROLLER_ID=<AgentControllerId> `
       AEMBIT_TENANT_ID=<TenantId> `
       AEMBIT_KERBEROS_ATTESTATION_ENABLED=true `
       SERVICE_LOGON_ACCOUNT=<NetBIOS domain name>\<sAMAccountName of gMSA>$
     ```

     If the account supplied in `SERVICE_LOGON_ACCOUNT` is not valid, you will receive the following message:

     > An error occurred while applying security settings. <`SERVICE_LOGON_ACCOUNT` value> is not a valid user or group. This could be a problem with the package, or a problem connecting to a domain controller on the network. Check your network connection and click Retry, or Cancel to end the install.

  3. When installing the Agent Proxy, make sure to set the `AEMBIT_AGENT_CONTROLLER` value as the DNS name component of the gMSA service principal.

  4. Make sure to add the [Kerberos Trust Provider](../../../access-policies/trust-providers/kerberos-trust-provider.md) in your Aembit Tenant.

HTTP proxy configuration

If your network routes outbound traffic through an HTTP proxy, configure the `HTTP_PROXY`, `HTTPS_PROXY`, and `NO_PROXY` environment variables for Agent Controller. See [Agent Controller environment variables](../../../../reference/edge-components/edge-component-env-vars.md#http_proxy) for details.

### Agent Controller environment variables

[Section titled “Agent Controller environment variables”](#agent-controller-environment-variables)

For a list of all available environment variables for configuring the Agent Controller installer, see [Agent Controller environment variables reference](../../../../reference/edge-components/edge-component-env-vars.md#agent-controller-environment-variables).

Security Best Practice

Make sure the Agent Controller can accept connections on port 5000 from Agent Proxies (update your security groups if needed). Because access to Agent Controller is sensitive, *your Agent Controller’s port should not be open to the Internet*.

### (Optional) Verify the service account

[Section titled “(Optional) Verify the service account”](#optional-verify-the-service-account)

By default, the Agent Controller service runs as the [`LocalService` account](https://learn.microsoft.com/en-us/windows/win32/services/localservice-account).

To verify that the Agent Controller service is running as the expected service account, use the following PowerShell command:

```powershell
(Get-WmiObject Win32_Service -Filter "Name='AembitAgentController'").StartName
```

If you don’t see the **Aembit Agent Controller** service running or if it’s running as a different user, [uninstall Agent Controller](#uninstall-agent-controller) and retry these instructions.

## Uninstall Agent Controller

[Section titled “Uninstall Agent Controller”](#uninstall-agent-controller)

To uninstall Agent Controller from your Windows Server, use Windows built-in **Add/Remove Programs** feature like you’d normally uninstall any other program or app from Windows.

## Limitations

[Section titled “Limitations”](#limitations)

Agent Controller on Windows has the following limitations:

* **Changing the service logon account after installation isn’t supported** -

  If you need to change to a different Windows service account, you must uninstall and reinstall the Agent Controller on your Windows Server host.

* **Changing the TLS strategy may not work as expected** -

  Because of the way Aembit stores and preserves parameters, changing from a TLS configuration using customer certificates to a configuration using Aembit-managed certificates may not work as expected. To remediate:

  1. Uninstall the Agent Controller.
  2. Delete the `C:\ProgramData\Aembit\AgentController` directory and its contents.
  3. Reinstall the Agent Controller.

## Installation details

[Section titled “Installation details”](#installation-details)

| **Attribute**       | **Value**                                                             |
| ------------------- | --------------------------------------------------------------------- |
| **Service name**    | `AembitAgentController`                                               |
| **Binary location** | `C:\Program Files\Aembit\AgentController\aembit_agent_controller.exe` |
| **Log files**       | `C:\ProgramData\Aembit\AgentController\Logs`                          |

## Additional resources

[Section titled “Additional resources”](#additional-resources)

* [Kerberos Trust Provider](../../../access-policies/trust-providers/kerberos-trust-provider.md)
