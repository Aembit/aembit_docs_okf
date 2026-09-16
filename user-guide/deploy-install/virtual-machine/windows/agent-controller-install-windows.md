---
type: how-to
title: "How to set up Agent Controller on Windows Server"
description: "How to set up Aembit Agent Controller on Windows Server"
resource: https://docs.aembit.io/user-guide/deploy-install/virtual-machine/windows/agent-controller-install-windows/
interface: web-ui
tags: ["windows", "virtual-machine", "deploy-install"]
timestamp: 2026-09-15T20:39:46-07:00
---

# How to set up Agent Controller on Windows Server

Aembit provides many different deployment options you can use to deploy Aembit Edge Components in your environment. Each of these options provide similar features and functionality. The steps for each of these options, however, are specific to the deployment option you select.

This page describes the process to deploy Agent Controller to a Windows Server virtual machine (VM).

> **Note**
>
> Aembit recommends deploying Agent Controller and Agent Proxy on standalone VMs and not collocating them on the same VM.
>
> See [About Colocating Aembit Edge Components](../../about-colocating-edge-components.md) for more info.

To install Agent Controller on Windows Server, Aembit provides a Windows installer file (`.msi`).\
See [Installation details](#installation-details) for more information about what it does.

Aembit supports three primary configurations when you install Agent Controller on Windows Server:

* A single Windows Server.

* A single Windows Server with Kerberos attestation enabled. See [Kerberos Trust Provider](../../../access-policies/trust-providers/kerberos-trust-provider.md).

* Multiple Windows Servers in a [high availability (HA) configuration](../../advanced-options/agent-controller/agent-controller-high-availability.md) using an Active Directory [Group Managed Service Account (gMSA)](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/manage/group-managed-service-accounts/group-managed-service-accounts/group-managed-service-accounts-overview). Using a gMSA reduces the operational difficulty in managing secrets across multiple Agent Controller hosts.

## Supported versions

Use the following table to make sure that Aembit supports the operating system and platform you’re deploying to your VM:

| Operating system    | Edge Component versions      |
| ------------------- | ---------------------------- |
| Windows Server 2019 | Agent Controller v1.21.2101+ |
| Windows Server 2022 | Agent Controller v1.21.2101+ |

## Prerequisites

Before you install Agent Controller on Windows Server, you must have the following:

* Network and system access to download and install software on the Windows Server host.

* If installing with Kerberos attestation enabled:

  * Your Agent Controller Windows Server host joined to an Active Directory (AD) domain.

## Install Agent Controller on Windows Server

To install an Aembit Agent Controller on Windows Server:

1. Download the latest release version of the Agent Controller installer from the [Agent Controller releases page](https://releases.aembit.io/agent_controller/index.html), making sure to replace the instances of `<version>` with the latest version in the following command. Note that downloading directly via a browser may result in unexpected behavior.

   ```powershell
   Invoke-WebRequest `
      -Uri https://releases.aembit.io/agent_controller/<version>/windows/amd64/aembit_agent_controller_windows_amd64_<version>.msi `
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

  2. Decide which hostname Agent Proxies use to reach this Agent Controller.

     Each Agent Proxy requests a Kerberos ticket for the Agent Controller’s Service Principal Name (SPN) `HTTP/<hostname>`. The Agent Controller service runs as the host’s computer account by default, and when the host joins the domain, Windows registers SPN entries on that computer account that cover the host’s own name. If Agent Proxies reach the Agent Controller by a different name, such as a DNS alias, register that SPN on the Agent Controller’s computer account from a domain-joined host with AD administrator privileges:

     ```powershell
     setspn -S HTTP/<alias> <Agent Controller computer name>
     ```

     To list every SPN registered on the Agent Controller’s computer account, run `setspn -L <Agent Controller computer name>`.

  3. Install the Agent Controller, using the following command. Make sure to replace `<TenantId>` with your Aembit Tenant ID and `<AgentControllerId>` with the ID of the Agent Controller you are configuring.

     ```powershell
     msiexec /i aembit_agent_controller.msi /l*v installer.log `
       AEMBIT_AGENT_CONTROLLER_ID=<AgentControllerId> `
       AEMBIT_TENANT_ID=<TenantId> `
       AEMBIT_KERBEROS_ATTESTATION_ENABLED=true
     ```

     > **Caution**
     >
     > When upgrading Agent Controller and you change the value of `SERVICE_LOGON_ACCOUNT`, then you must restart the Agent Controller service once installation completes.

  4. Make sure to add the [Kerberos Trust Provider](../../../access-policies/trust-providers/kerberos-trust-provider.md) in your Aembit Tenant.

  5. When installing the Agent Proxy, set the host part of `AEMBIT_AGENT_CONTROLLER` to the hostname in the SPN, for example `AEMBIT_AGENT_CONTROLLER=http://<hostname>:5000`. An IP address doesn’t work, because no SPN matches it.

* Agent Controllers + Kerberos attestation + gMSA

  2. Create a [Group Managed Service Account (gMSA)](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/manage/group-managed-service-accounts/group-managed-service-accounts/getting-started-with-group-managed-service-accounts) in AD and allow every Agent Controller host to retrieve its password. Every Agent Controller in the cluster runs as this gMSA.

  3. Register the SPN `HTTP/<Load Balancer hostname>` on the gMSA:

     ```powershell
     setspn -S HTTP/<Load Balancer hostname> <gMSA name>
     ```

     You don’t need an SPN for each individual Agent Controller host.

  4. Install Agent Controller on each host, using the following command. Run the `.msi` installer to enable Trust Provider-based Agent Controller registration, making sure to replace `<AgentControllerId>` and `<TenantId>` with the values from your Aembit Tenant.

     To install Agent Controller on Windows Server using a gMSA, you must also set the `SERVICE_LOGON_ACCOUNT` environment variable using [Down-Level Logon Name format](https://learn.microsoft.com/en-us/windows/win32/secauthn/user-name-formats#down-level-logon-name) `SERVICE_LOGON_ACCOUNT=<NetBIOS domain name>\\<sAMAccountName of gMSA>`. Use the same gMSA on every host.

     ```powershell
     msiexec /i aembit_agent_controller.msi /l*v installer.log `
       AEMBIT_AGENT_CONTROLLER_ID=<AgentControllerId> `
       AEMBIT_TENANT_ID=<TenantId> `
       AEMBIT_KERBEROS_ATTESTATION_ENABLED=true `
       SERVICE_LOGON_ACCOUNT=<NetBIOS domain name>\<sAMAccountName of gMSA>$
     ```

     > **Note**
     >
     > If the account in `SERVICE_LOGON_ACCOUNT` isn’t valid, the installer shows the following message:
     >
     > ```text
     > An error occurred while applying security settings. <SERVICE_LOGON_ACCOUNT value> is not a valid user or group.
     > This could be a problem with the package, or a problem connecting to a domain controller on the network.
     > Check your network connection and click Retry, or Cancel to end the install.
     > ```
     >
     > If the gMSA exists but the host can’t retrieve its password yet, click **Retry** after the host has contacted the Domain Controller.

  5. When installing the Agent Proxy, set the host part of `AEMBIT_AGENT_CONTROLLER` to the load balancer hostname in the SPN, for example `AEMBIT_AGENT_CONTROLLER=http://<Load Balancer hostname>:5000`.

  6. Make sure to add the [Kerberos Trust Provider](../../../access-policies/trust-providers/kerberos-trust-provider.md) in your Aembit Tenant and select every Agent Controller in the cluster in its **Agent Controller** field.

> **HTTP proxy configuration**
>
> If your network routes outbound traffic through an HTTP proxy, configure the `HTTP_PROXY`, `HTTPS_PROXY`, and `NO_PROXY` environment variables for Agent Controller. See [Agent Controller environment variables](../../../../reference/edge-components/edge-component-env-vars.md#http_proxy) for details.

### Agent Controller environment variables

For a list of all available environment variables for configuring the Agent Controller installer, see [Agent Controller environment variables reference](../../../../reference/edge-components/edge-component-env-vars.md#agent-controller-environment-variables).

> **Security Best Practice**
>
> Make sure the Agent Controller can accept connections on port 5000 from Agent Proxies (update your security groups if needed). Because access to Agent Controller is sensitive, *your Agent Controller’s port shouldn’t be open to the Internet*.

### (Optional) Verify the service account

By default, the Agent Controller service runs as the [`LocalService` account](https://learn.microsoft.com/en-us/windows/win32/services/localservice-account).

To verify that the Agent Controller service is running as the expected service account, use the following PowerShell command:

```powershell
(Get-WmiObject Win32_Service -Filter "Name='AembitAgentController'").StartName
```

If you don’t see the **Aembit Agent Controller** service running or if it’s running as a different user, [uninstall Agent Controller](#uninstall-agent-controller) and retry these instructions.

## Uninstall Agent Controller

To uninstall Agent Controller from your Windows Server, use Windows built-in **Add/Remove Programs** feature like you’d normally uninstall any other program or app from Windows.

## Limitations

Agent Controller on Windows has the following limitations:

* **The Kerberos Trust Provider can’t register the Agent Controller** -

  The Agent Controller attests Agent Proxies for the Kerberos Trust Provider and can’t attest itself the same way. Register a domain-joined Agent Controller on premises with a Device Code. When the host runs in AWS, Azure, or Kubernetes, register it with an AWS Role, AWS Metadata Service, Azure Metadata Service, or Kubernetes Service Account Trust Provider instead.

* **Changing the service logon account after installation isn’t supported** -

  If you need to change to a different Windows service account, you must uninstall and reinstall the Agent Controller on your Windows Server host.

* **Changing the TLS strategy may not work as expected** -

  Because of the way Aembit stores and preserves parameters, changing from a TLS configuration using customer certificates to a configuration using Aembit-managed certificates may not work as expected. To remediate:

  1. Uninstall the Agent Controller.
  2. Delete the `C:\ProgramData\Aembit\AgentController` directory and its contents.
  3. Reinstall the Agent Controller.

## Installation details

| **Attribute**       | **Value**                                                             |
| ------------------- | --------------------------------------------------------------------- |
| **Service name**    | `AembitAgentController`                                               |
| **Binary location** | `C:\Program Files\Aembit\AgentController\aembit_agent_controller.exe` |
| **Log files**       | `C:\ProgramData\Aembit\AgentController\Logs`                          |

## Additional resources

* [Kerberos Trust Provider](../../../access-policies/trust-providers/kerberos-trust-provider.md)
