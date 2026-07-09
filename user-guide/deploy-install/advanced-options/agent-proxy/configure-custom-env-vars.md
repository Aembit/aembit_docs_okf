---
type: how-to
title: "Configure custom environment variables for Agent Proxy"
description: "How to inject custom environment variables into Agent Proxy and Aembit CLI process so OIDC and JWT-SVID dynamic claims can read them."
resource: https://docs.aembit.io/user-guide/deploy-install/advanced-options/agent-proxy/configure-custom-env-vars/
interface: web-ui
tags: [agent-proxy, advanced-option, deploy-install]
timestamp: 2026-06-05T14:13:51-07:00
---

# Configure custom environment variables for Agent Proxy

[OIDC and JWT-SVID dynamic claims](../../../access-policies/credential-providers/advanced-options/dynamic-claims-oidc.md) can include values from environment variables present in **Agent Proxy** or **Aembit CLI** process environments. By default, Agent Proxy and Aembit CLI capture no custom variables. To enable capture, you must set an allowlist and make sure the variables are present in the process environment.

This page describes how to inject custom variables and configure the allowlist on each supported platform.

## How capture works

[Section titled “How capture works”](#how-capture-works)

A custom environment variable becomes available to dynamic claims only when both of the following are true:

1. The variable is present in the **Agent Proxy** or **Aembit CLI** process environment.
2. The variable name appears in [`AEMBIT_ENV_VAR_ALLOWLIST`](../../../../reference/edge-components/edge-component-env-vars.md#aembit_env_var_allowlist), a comma-separated list of permitted variable names.

If a Credential Provider references a missing variable (absent from the process environment or the allowlist), Agent Proxy logs a warning (`requested env variable <name> is not in allow list`) and omits the variable from the credential request. The credential request still proceeds, but without that claim value.

A small set of always-available variables (such as `K8S_POD_NAME` and `AEMBIT_RESOURCE_SET_ID`) bypass the allowlist requirement. See [Always-available variables](#always-available-variables).

## Set the allowlist

[Section titled “Set the allowlist”](#set-the-allowlist)

Set [`AEMBIT_ENV_VAR_ALLOWLIST`](../../../../reference/edge-components/edge-component-env-vars.md#aembit_env_var_allowlist) to a comma-separated list of variable names that you want Aembit to capture.

```shell
AEMBIT_ENV_VAR_ALLOWLIST=CORPORATE_APP_ID,WEBSITE_HOSTNAME,AWS_LAMBDA_FUNCTION_NAME
```

The allowlist must live in the same process environment as Agent Proxy or Aembit CLI itself. Where you set it depends on how you deploy each one:

* **Linux VM**: pass `AEMBIT_ENV_VAR_ALLOWLIST=<value>` to Agent Proxy installer, or add it to a `systemd` drop-in for Agent Proxy service
* **Windows VM**: pass `AEMBIT_ENV_VAR_ALLOWLIST=<value>` as an MSI property to `msiexec`, or set it as a machine-level environment variable
* **Kubernetes**: set it under `agentProxy.env` in your Helm values
* **Aembit CLI**: export it in the shell that runs Aembit CLI

For the exact commands on each platform, see the following [Configure by deployment](#configure-by-deployment) section.

Aembit matches variable names exactly and treats them as case-sensitive.

Windows

Windows resolves OS-level environment variables case-insensitively, so a process may receive `my_var` from a system-level `MY_VAR`. Aembit’s allowlist match itself is still case-sensitive, so the name in `AEMBIT_ENV_VAR_ALLOWLIST` must match the exact case Agent Proxy process sees.

## Configure by deployment

[Section titled “Configure by deployment”](#configure-by-deployment)

Choose the tab for your deployment platform.

* Linux VM (Agent Proxy)

  On Linux, Agent Proxy runs as a `systemd` service. Custom environment variables must live in the service’s environment, which means they go in a `systemd` drop-in file. Agent Proxy installer doesn’t accept custom variables directly, but it does accept `AEMBIT_ENV_VAR_ALLOWLIST` as an installer environment variable.

  You have three options for getting your custom variables and the allowlist into the service environment. The first option is the recommended path for most deployments.

  #### Option 1: `systemctl edit` drop-in Recommended

  [Section titled “Option 1: systemctl edit drop-in ”](#option-1-systemctl-edit-drop-in-)

  Use `systemctl edit` to create a drop-in override that `systemd` applies on top of the shipped unit file.

  1. Install Agent Proxy normally (without custom variables):

     ```shell
     sudo AEMBIT_AGENT_CONTROLLER=http://<agent-controller-host>:5000 ./install
     ```

  2. Open a drop-in editor for Agent Proxy service:

     ```shell
     sudo systemctl edit aembit_agent_proxy
     ```

  3. Add an `[Service]` block with the allowlist and your custom variables, then save and exit:

     ```ini
     [Service]
     Environment="AEMBIT_ENV_VAR_ALLOWLIST=CORPORATE_APP_ID,WEBSITE_HOSTNAME"
     Environment="CORPORATE_APP_ID=app-1234"
     Environment="WEBSITE_HOSTNAME=example.com"
     ```

  4. Restart Agent Proxy service so it picks up the updated environment:

     ```shell
     sudo systemctl restart aembit_agent_proxy
     ```

  5. Confirm the running service has the variables you expect:

     ```shell
     systemctl show aembit_agent_proxy --property=Environment
     ```

  `systemctl edit` writes the override to `/etc/systemd/system/aembit_agent_proxy.service.d/override.conf`, which survives package upgrades.

  #### Option 2: manually managed drop-in file

  [Section titled “Option 2: manually managed drop-in file”](#option-2-manually-managed-drop-in-file)

  If you provision VMs with Ansible, Chef, Puppet, or another configuration-management tool, write a drop-in file directly so the tool can manage it as a regular file resource.

  1. Create the drop-in directory:

     ```shell
     sudo mkdir -p /etc/systemd/system/aembit_agent_proxy.service.d
     ```

  2. Write a drop-in file (any `.conf` filename works) containing your `[Service]` overrides:

     /etc/systemd/system/aembit\_agent\_proxy.service.d/aembit-env.conf

     ```ini
     [Service]
     Environment="AEMBIT_ENV_VAR_ALLOWLIST=CORPORATE_APP_ID,WEBSITE_HOSTNAME"
     Environment="CORPORATE_APP_ID=app-1234"
     Environment="WEBSITE_HOSTNAME=example.com"
     ```

  3. Reload `systemd` and restart Agent Proxy service:

     ```shell
     sudo systemctl daemon-reload
     sudo systemctl restart aembit_agent_proxy
     ```

  #### Option 3: pass the allowlist at install time, set custom variables in a drop-in

  [Section titled “Option 3: pass the allowlist at install time, set custom variables in a drop-in”](#option-3-pass-the-allowlist-at-install-time-set-custom-variables-in-a-drop-in)

  Agent Proxy installer accepts `AEMBIT_ENV_VAR_ALLOWLIST` as an installer environment variable, so you can configure the allowlist at install time and put your custom variables in a drop-in afterward.

  1. Install Agent Proxy with `AEMBIT_ENV_VAR_ALLOWLIST` set:

     ```shell
     sudo AEMBIT_AGENT_CONTROLLER=http://<agent-controller-host>:5000 \
       AEMBIT_ENV_VAR_ALLOWLIST=CORPORATE_APP_ID,WEBSITE_HOSTNAME \
       ./install
     ```

  2. Create a drop-in for your custom variables (using either of the preceding options):

     ```ini
     [Service]
     Environment="CORPORATE_APP_ID=app-1234"
     Environment="WEBSITE_HOSTNAME=example.com"
     ```

  3. Restart Agent Proxy service:

     ```shell
     sudo systemctl restart aembit_agent_proxy
     ```

* Windows VM (Agent Proxy)

  On Windows, Agent Proxy runs as a Windows service. Set custom variables as machine-level environment variables so the service inherits them on start. Agent Proxy MSI installer accepts `AEMBIT_ENV_VAR_ALLOWLIST` as an MSI property at install time.

  1. Install Agent Proxy with `AEMBIT_ENV_VAR_ALLOWLIST` set as an MSI property:

     ```powershell
     msiexec /i aembit_agent_proxy_windows_amd64_<version>.msi /l*v install.log ^
       AEMBIT_ENV_VAR_ALLOWLIST=CORPORATE_APP_ID,WEBSITE_HOSTNAME
     ```

  2. In an elevated PowerShell session, set each custom variable at the machine scope:

     ```powershell
     [Environment]::SetEnvironmentVariable('CORPORATE_APP_ID', 'app-1234', 'Machine')
     [Environment]::SetEnvironmentVariable('WEBSITE_HOSTNAME', 'example.com', 'Machine')
     ```

  3. Restart Agent Proxy service so it picks up the new machine-level variables:

     ```powershell
     Restart-Service aembit_agent_proxy
     ```

  4. Confirm the variables are visible to the service:

     ```powershell
     [Environment]::GetEnvironmentVariable('CORPORATE_APP_ID', 'Machine')
     [Environment]::GetEnvironmentVariable('WEBSITE_HOSTNAME', 'Machine')
     ```

  To remove a custom variable, set it to `$null` at the machine scope and restart the service:

  ```powershell
  [Environment]::SetEnvironmentVariable('CORPORATE_APP_ID', $null, 'Machine')
  Restart-Service aembit_agent_proxy
  ```

  You can also set or change `AEMBIT_ENV_VAR_ALLOWLIST` after install time using the same `SetEnvironmentVariable(..., 'Machine')` approach.

* Kubernetes (Agent Proxy)

  In a Kubernetes deployment, set custom variables on Agent Proxy container through the Helm chart’s `agentProxy.env` block, then reference them in `AEMBIT_ENV_VAR_ALLOWLIST`.

  1. Add custom variables and the allowlist to your Helm values:

     ```yaml
     agentProxy:
       env:
         AEMBIT_ENV_VAR_ALLOWLIST: "CORPORATE_APP_ID,WEBSITE_HOSTNAME"
         CORPORATE_APP_ID: "app-1234"
         WEBSITE_HOSTNAME: "example.com"
     ```

  2. Apply the values to your release:

     ```shell
     helm upgrade --install aembit-edge aembit/aembit-edge -f values.yaml
     ```

  3. Confirm the variables are visible in the running pod:

     ```shell
     kubectl exec -it <agent-proxy-pod> -- env | grep -E '^(CORPORATE_APP_ID|WEBSITE_HOSTNAME|AEMBIT_ENV_VAR_ALLOWLIST)='
     ```

  The Aembit Helm chart automatically injects the [always-available variables](#always-available-variables) `K8S_POD_NAME` and `K8S_NAMESPACE` from the Kubernetes downward API (via `fieldRef: metadata.name` and `fieldRef: metadata.namespace` in the chart’s webhook template), so you don’t need to set them yourself. You can set [`KUBERNETES_PROVIDER_ID`](../../../../reference/edge-components/edge-component-env-vars.md#kubernetes_provider_id) under `agentProxy.env` to identify the cluster.

* Aembit CLI

  Aembit CLI captures variables that are present in its own process environment. Export the variables and the allowlist in the same shell that runs Aembit CLI:

  ```shell
  export AEMBIT_ENV_VAR_ALLOWLIST=CORPORATE_APP_ID,WEBSITE_HOSTNAME
  export CORPORATE_APP_ID=app-1234
  export WEBSITE_HOSTNAME=example.com


  aembit <command>
  ```

  For persistent configuration, set these variables in your shell profile (Linux) or as user/system environment variables (Windows).

## Verify Aembit captures a custom variable

[Section titled “Verify Aembit captures a custom variable”](#verify-aembit-captures-a-custom-variable)

After you configure a custom variable, verify that a dynamic claim can read it:

1. Add a temporary Custom Claim to an existing OIDC ID Token Credential Provider, for example:

   * **Claim Name**: `test_custom_env`
   * **Value**: `${os.environment.CORPORATE_APP_ID}`

2. Trigger a credential request from a Client Workload.

3. Decode the issued token (for example, with [jwt.io](https://jwt.io)) and confirm the `test_custom_env` claim contains the expected value.

4. Remove the temporary Custom Claim once you’ve confirmed capture works.

If the claim is empty, see [Troubleshooting](#troubleshooting).

## Always-available variables

[Section titled “Always-available variables”](#always-available-variables)

Dynamic claims can read the following variables regardless of `AEMBIT_ENV_VAR_ALLOWLIST`, provided each one exists in the process environment. Reference them in dynamic claims with `${os.environment.<NAME>}`, for example `${os.environment.CLIENT_WORKLOAD_ID}`.

### Kubernetes-only

[Section titled “Kubernetes-only”](#kubernetes-only)

Aembit populates these variables only on Kubernetes deployments:

| Variable                                                                                              | Typical source                          |
| ----------------------------------------------------------------------------------------------------- | --------------------------------------- |
| [`K8S_POD_NAME`](../../../../reference/edge-components/edge-component-env-vars.md#k8s_pod_name)                     | Injected by the Aembit Helm chart       |
| [`K8S_NAMESPACE`](../../../../reference/edge-components/edge-component-env-vars.md#k8s_namespace)                   | Injected by the Aembit Helm chart       |
| [`KUBERNETES_PROVIDER_ID`](../../../../reference/edge-components/edge-component-env-vars.md#kubernetes_provider_id) | Set by the customer in `agentProxy.env` |

### All deployments

[Section titled “All deployments”](#all-deployments)

Aembit populates these variables on Linux Virtual Machines, Windows Virtual Machines, and Kubernetes:

| Variable                                                                                              | Typical source                       |
| ----------------------------------------------------------------------------------------------------- | ------------------------------------ |
| [`AEMBIT_RESOURCE_SET_ID`](../../../../reference/edge-components/edge-component-env-vars.md#aembit_resource_set_id) | Set during install or in Helm values |
| [`CLIENT_WORKLOAD_ID`](../../../../reference/edge-components/edge-component-env-vars.md#client_workload_id)         | Set per workload                     |

## Behavior and scope

[Section titled “Behavior and scope”](#behavior-and-scope)

### Process boundary

[Section titled “Process boundary”](#process-boundary)

Aembit reads environment variables only from the Agent Proxy or Aembit CLI process environment. Variables set only in the Client Workload process aren’t visible to dynamic claims. Agent Proxy and Aembit CLI act as the boundary.

### Supported platforms

[Section titled “Supported platforms”](#supported-platforms)

Aembit captures custom environment variables on:

* **Agent Proxy**: Linux Virtual Machines, Windows Virtual Machines, and Kubernetes
* **Aembit CLI**: Linux and Windows Virtual Machines

## Troubleshooting

[Section titled “Troubleshooting”](#troubleshooting)

| Symptom                                                                                        | Likely cause                                                | Resolution                                                                                                                                |
| ---------------------------------------------------------------------------------------------- | ----------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| Claim value is empty                                                                           | Variable missing from the process environment               | Confirm with `systemctl show` (Linux), `[Environment]::GetEnvironmentVariable(..., 'Machine')` (Windows), or `kubectl exec ... env` (K8s) |
| Claim value is empty and Agent Proxy logs `requested env variable <name> is not in allow list` | Variable name not in `AEMBIT_ENV_VAR_ALLOWLIST`             | Add the name to the allowlist and restart the service                                                                                     |
| Variable visible in shell but not in claim                                                     | Set in Client Workload process, not Agent Proxy/CLI process | Move the variable definition to Agent Proxy or Aembit CLI process environment                                                             |

## Related docs

[Section titled “Related docs”](#related-docs)

* [OIDC and JWT-SVID dynamic claims](../../../access-policies/credential-providers/advanced-options/dynamic-claims-oidc.md)
* [Edge Component environment variables reference](../../../../reference/edge-components/edge-component-env-vars.md)
* [How to set up Agent Proxy on a Linux VM](../../virtual-machine/linux/agent-proxy-install-linux.md)
* [How to set up Agent Proxy on Windows Server](../../virtual-machine/windows/agent-proxy-install-windows.md)
