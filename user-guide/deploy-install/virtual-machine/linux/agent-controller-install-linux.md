---
type: how-to
title: "How to set up Agent Controller on Linux"
description: "How to set up Aembit Agent Controller on Linux"
resource: https://docs.aembit.io/user-guide/deploy-install/virtual-machine/linux/agent-controller-install-linux/
interface: web-ui
tags: ["linux", "virtual-machine", "deploy-install"]
timestamp: 2026-09-08T23:32:41-07:00
---

# How to set up Agent Controller on Linux

Aembit provides many different deployment options you can use to deploy Aembit Edge Components in your environment. Each of these options provide similar features and functionality. The steps for each of these options, however, are specific to the deployment option you select.

This page describes the process to deploy Agent Controller to a Linux virtual machine (VM).

> **Note**
>
> Aembit recommends deploying Agent Controller and Agent Proxy on standalone VMs and not collocating them on the same VM.
>
> See [About Colocating Aembit Edge Components](../../about-colocating-edge-components.md) for more info.

## Supported versions

Use the following table to make sure that Aembit supports the operating system and platform you’re deploying to your VM:

| Operating system | Edge Component versions     |
| ---------------- | --------------------------- |
| Ubuntu 20.04 LTS | Agent Controller v1.12.878+ |
| Ubuntu 22.04 LTS | Agent Controller v1.12.878+ |
| Red Hat 8.9 \*   | Agent Controller v1.12.878+ |

\* See [How to configure Agent Proxy on SELinux or RHEL](agent-proxy-selinux-config.md) for more info.

## Install Agent Controller

To install Agent Controller, follow these steps:

1. Download the latest [Agent Controller Release](https://releases.aembit.io/agent_controller/index.html).

2. Log on to the remote host with your user:

   ```shell
   ssh -i <path/to/your/ssh/key> <your-username or remote-host>@<hostname>
   ```

3. Download Agent Controller using the correct `<version_number>`:

   ```shell
   wget https://releases.aembit.io/agent_controller/<version_number>/linux/amd64/aembit_agent_controller_linux_amd64_<version_number>.tar.gz
   ```

4. Unpack the archive:

   ```shell
   tar xf aembit_agent_controller_linux_amd64_<version_number>.tar.gz
   ```

5. Go to the unpacked directory:

   ```shell
   cd aembit_agent_controller_linux_amd64
   ```

6. Run the installer to enable Trust Provider-based Agent Controller registration, making sure to replace `<TenantID>` and `<AgentControllerId>` with the values from your Aembit Tenant:

   ```shell
   sudo AEMBIT_TENANT_ID=<TenantID> AEMBIT_AGENT_CONTROLLER_ID=<AgentControllerId> ./install
   ```

   Optionally, add any other [Agent Controller environment variables reference](../../../../reference/edge-components/edge-component-env-vars.md#agent-controller-environment-variables) in the format `ENV_VAR_NAME=myvalue`.

   > **Trust Providers**
   >
   > If you don’t already have a Trust Provider, see [Add Trust Provider](../../../access-policies/trust-providers/add-trust-provider.md).
   >
   > Popular Trust Providers:
   >
   > * [AWS Metadata Service](../../../access-policies/trust-providers/aws-metadata-service-trust-provider.md)
   > * [Azure Instance Metadata Service](../../../access-policies/trust-providers/azure-metadata-service-trust-provider.md)

   > **Device Codes vs Trust Providers**
   >
   > Device Codes are a fallback authentication method for the Agent Controller, and they’re less secure than Trust Providers. Prefer a Trust Provider for production deployments, since Trust Providers offer stronger, automated attestation with better control and flexibility.
   >
   > Use a Device Code only in these cases:
   >
   > * A test, proof-of-concept, lab, or demo environment.
   > * A production environment that has no Trust-Provider-based attestation available, such as a bare-metal or on-premises host, or VMware vSphere without cloud metadata services. In these environments, Device Code is the supported Agent Controller registration method.
   >
   > See [About the Aembit Agent Controller](../../about-agent-controller.md) for more information.

   To use a Device Code, you must generate a Device Code in the Aembit website UI and replace `AEMBIT_AGENT_CONTROLLER_ID` with the `AEMBIT_DEVICE_CODE` environmental variable in the preceding command.

> **HTTP proxy configuration**
>
> If your network routes outbound traffic through an HTTP proxy, configure the `HTTP_PROXY`, `HTTPS_PROXY`, and `NO_PROXY` environment variables for Agent Controller. See [Agent Controller environment variables](../../../../reference/edge-components/edge-component-env-vars.md#http_proxy) for details.

### Agent Controller environment variables

For a list of all available environment variables for configuring the Agent Controller installer, see [Agent Controller environment variables reference](../../../../reference/edge-components/edge-component-env-vars.md#agent-controller-environment-variables).

> **Security Best Practice**
>
> Make sure the Agent Controller can accept connections on port 5000 from Agent Proxies (update your security groups if needed). Because access to Agent Controller is sensitive, *your Agent Controller’s port should not be open to the Internet*.

### Uninstall Agent Controller

Run the following command to uninstall the previously installed Agent Controller.

```shell
sudo ./uninstall
```
