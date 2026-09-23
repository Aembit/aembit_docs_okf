---
type: how-to
title: "How to set up Agent Proxy on a Linux VM"
description: "How to set up Aembit Agent Proxy on a Linux virtual machine (VM)"
resource: https://docs.aembit.io/user-guide/deploy-install/virtual-machine/linux/agent-proxy-install-linux/
interface: web-ui
tags: ["linux", "virtual-machine", "deploy-install"]
timestamp: 2026-09-22T11:51:31-07:00
---

# How to set up Agent Proxy on a Linux VM

Aembit provides many different deployment options you can use to deploy Aembit Edge Components in your environment. Each of these options provide similar features and functionality. The steps for each of these options, however, are specific to the deployment option you select.

This page describes the process to deploy Agent Proxy to a Linux virtual machine (VM).

> **Note**
>
> Aembit recommends deploying Agent Controller and Agent Proxy on standalone VMs and not collocating them on the same VM.
>
> See [About Colocating Aembit Edge Components](../../about-colocating-edge-components.md) for more info.

## Supported versions

Use the following table to make sure that Aembit supports the operating system and platform you’re deploying to your VM:

| Operating system | Edge Component versions |
| ---------------- | ----------------------- |
| Ubuntu 20.04 LTS | Agent Proxy v1.11.1551+ |
| Ubuntu 22.04 LTS | Agent Proxy v1.11.1551+ |
| Ubuntu 24.04 LTS | Agent Proxy v1.11.1551+ |
| Ubuntu 26.04 LTS | Agent Proxy v1.34.5960+ |
| Red Hat 8.9 \*   | Agent Proxy v1.11.1551+ |

\* See [How to configure Agent Proxy on SELinux or RHEL](agent-proxy-selinux-config.md) for more info.

## Install Agent Proxy

To install Agent Proxy on Linux, follow these steps:

1. Download the latest [Agent Proxy Release](https://releases.aembit.io/agent_proxy/index.html).

2. Log on to the VM with your username:

   ```shell
   ssh -i <path/to/your/ssh/key> <your-username or remote-host>@<hostname>
   ```

3. Download the latest released version of Agent Proxy. Make sure to include the `<version_number>` in the command:

   ```shell
   wget https://releases.aembit.io/agent_proxy/<version_number>/linux/amd64/aembit_agent_proxy_linux_amd64_<version_number>.tar.gz
   ```

4. Unpack the archive using the correct *version number* in the command:

   ```shell
   tar xf aembit_agent_proxy_linux_amd64_<version_number>.tar.gz
   ```

5. Navigate to the unpacked directory:

   ```shell
   cd aembit_agent_proxy_linux_amd64_<version_number>
   ```

6. Run the Agent Proxy installer, making sure to replace `<agent-controller-host>` address:

   ```shell
   sudo AEMBIT_AGENT_CONTROLLER=http://<agent-controller-host>:5000 ./install
   ```

   Optionally, add any other [Agent Proxy environment variables reference](../../../../reference/edge-components/edge-component-env-vars.md#agent-proxy-environment-variables) in the format `ENV_VAR_NAME=myvalue`.

   > **Kerberos attestation**
   >
   > To attest this VM with the [Kerberos Trust Provider](../../../access-policies/trust-providers/kerberos-trust-provider.md), add `AEMBIT_KERBEROS_ATTESTATION_ENABLED=true` and either `AEMBIT_PRIVILEGED_KEYTAB=true` or `AEMBIT_AGENT_PROXY_KERBEROS_PRINCIPAL=<principal>` to the install command. The host part of `AEMBIT_AGENT_CONTROLLER` must match the hostname in the Agent Controller’s service principal name.

7. (Optional) You may optionally use the additional installation environment variable `AEMBIT_DOCKER_CONTAINER_CIDR`. Set this variable to the CIDR block of the Docker container bridge network so Agent Proxy can handle workloads running in containers on your VM.

   Your Client Workloads running on your virtual machine should now be able to access server workloads.

> **Note**
>
> If you are running Aembit in AWS, you may use the Agent Controller Private IP DNS name as Agent Controller Host (for example, `ip-172-31-3-73.us-west-1.compute.internal`).

## Agent Proxy environment variables

For a list of all available environment variables for configuring the Agent Proxy installer, see [Agent Proxy environment variables reference](../../../../reference/edge-components/edge-component-env-vars.md#agent-proxy-environment-variables).

## Uninstall Agent Proxy

Run the following command to uninstall Agent Proxy from Linux VMs:

```shell
sudo ./uninstall
```

## Access Agent Proxy logs

To access logs on your Agent Proxy, select the following tab for your operating system:

Linux handles Agent Proxy logs with `journald`. To access Agent Proxy logs, run:

```shell
journalctl --namespace aembit_agent_proxy
```

Older versions of `journald` do not support namespaces. If the preceding command doesn’t work, you can use the following command:

```shell
journalctl --unit aembit_agent_proxy
```

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

To make custom environment variables available to [dynamic claims](../../../access-policies/credential-providers/advanced-options/dynamic-claims.md), add them to a `systemd` drop-in for Agent Proxy service and list each variable name in [`AEMBIT_ENV_VAR_ALLOWLIST`](../../../../reference/edge-components/edge-component-env-vars.md#aembit_env_var_allowlist).

For step-by-step instructions (including the recommended `systemctl edit` workflow), see [Configure custom environment variables for Agent Proxy](../../advanced-options/agent-proxy/configure-custom-env-vars.md#configure-by-deployment).

### Configuring gRPC keep-alives

Agent Proxy can send gRPC keep-alive messages on its connection to your Aembit Tenant. Enable them at install time for restrictive networks, such as a Secure Web Gateway that drops idle connections without closing them. Set both `AEMBIT_TENANT_GRPC_PING_INTERVAL_SECS` and `AEMBIT_TENANT_GRPC_PING_TIMEOUT_SECS`:

```shell
sudo AEMBIT_AGENT_CONTROLLER=http://<agent-controller-host>:5000 \
  AEMBIT_TENANT_GRPC_PING_INTERVAL_SECS=30 \
  AEMBIT_TENANT_GRPC_PING_TIMEOUT_SECS=10 ./install
```

These settings are off by default. For details, see [Agent Proxy environment variables reference](../../../../reference/edge-components/edge-component-env-vars.md#agent-proxy-environment-variables).
