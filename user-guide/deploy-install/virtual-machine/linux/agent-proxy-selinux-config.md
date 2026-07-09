---
type: how-to
title: "How to configure Agent Proxy on SELinux or RHEL"
description: "How configure Agent Proxy on SELinux or RedHat Enterprise Linux (RHEL)"
resource: https://docs.aembit.io/user-guide/deploy-install/virtual-machine/linux/agent-proxy-selinux-config/
interface: web-ui
tags: [linux, virtual-machine, deploy-install]
timestamp: 2025-05-29T11:26:12-07:00
type_inferred: true
---

# How to configure Agent Proxy on SELinux or RHEL

Security Enhanced Linux (SELinux) is a mandatory-access security tool that enables administrators to strictly define how processes are able to interact with system resources like files, directories, and sockets. For a thorough introduction to SELinux, see the [RedHat SELinux page](https://www.redhat.com/en/topics/linux/what-is-selinux) and the [SELinux Wiki](https://selinuxproject.org/page/Main_Page).

For SELinux users on RedHat Enterprise Linux, Aembit Edge Components ship with SELinux rules (`.te`) files when deployed to VM environments. Use `.te` files to create a custom SELinux policy.

On this page:

* How to [create a custom SELinux policy](#create-an-selinux-policy) for Edge Components deployed on a RHEL 8 or RHEL 9 VM.

* How to [update your Edge Component’s policy](#selinux-policy-updates) in case SELinux raises violations.

* How to [migrate your existing Edge Component policy](#edge-component-version-upgrades) when updating the installed version your Edge Component.

## Create an SELinux Policy

[Section titled “Create an SELinux Policy”](#create-an-selinux-policy)

To configure SELinux to work with Aembit Edge Components, perform the following steps:

Note

These steps assume you’ve already installed an Edge Component on your RHEL virtual machine. If you haven’t deployed Aembit Edge Components, please see the [Virtual Machine guide](../overview.md) to get started.

Note

Aembit recommends switching SELinux to permissive mode before installing a new SELinux policy. You can do this temporarily (until the system reboots) by executing `sudo setenforce 0`. To make the change persistent, you can:

1. Modify `/etc/selinux/config` and set the `SELINUX=` line to `SELINUX=permissive`.

2. Reboot the machine.

1) Install the requisite SELinux packages.

   ```shell
   sudo dnf install -y selinux-policy-devel rpm-build
   ```

2) Create a new directory to contain the SELinux policy files.

   ```shell
   mkdir ~/edge_component_policy
   cd ~/edge_component_policy
   ```

   Note

   The following steps use Agent Proxy as the example application. If you’re installing a policy for Agent Controller, replace occurrences of `proxy` with `controller` in script and/or directory names.

3) Use the `selinux/generate_selinux_policy.sh` script inside your Edge Component installer bundle to generate a new SELinux policy for the Edge Component.

   \~/edge\_component\_policy

   ```shell
   sudo <path_to_installer_bundle>/selinux/generate_selinux_policy.sh
   # e.g sudo /home/user/aembit_agent_proxy_linux_amd64_1.19.2326/selinux/generate_selinux_policy.sh
   ```

   Note

   Your current working directory should now contain a number of new files, including:

   * `aembit_agent_proxy.te`

   * `aembit_agent_proxy.if`

   * `aembit_agent_proxy.fc`

   * `aembit_agent_proxy.sh`

4) Copy the `.te` file for your RedHat version, located in the Edge Component installer bundle’s `selinux` directory, into the directory with the newly generated policy files.

   Intended behavior

   This step replaces the generated `aembit_agent_proxy.te` file in your working directory with the one provided in the Edge Component installer bundle.

   \~/edge\_component\_policy

   ```shell
   sudo cp <path_to_installer_bundle>/selinux/<RHEL_version>/aembit_agent_proxy.te .
   # e.g sudo cp /home/user/aembit_agent_proxy_linux_amd64_1.19.2326/selinux/RHEL_9.3/aembit_agent_proxy.te .
   ```

5) Install the policy using the generated `aembit_agent_proxy.sh` shell script.

   \~/edge\_component\_policy

   ```shell
   sudo ./aembit_agent_proxy.sh
   ```

6) Restart the Edge Component for the policy to take effect.

   ```shell
   sudo systemctl restart aembit_agent_proxy
   # or sudo systemctl restart aembit_agent_controller
   ```

7) Verify Agent Proxy is now running under SELinux.

   ```shell
   ps -efZ | grep aembit_agent_proxy
   # Sample output:
   # system_u:system_r:aembit_agent_proxy_t:s0 [...] /opt/aembit/edge/agent_proxy/<version>/bin/aembit_agent_proxy
   # ^^^^^^^^ ^^^^^^^^ ^^^^^^^^^^^^^^^^^^^^ - SELinux-generated user, role, and type for the Agent Proxy binary
   ```

After completing the preceding steps, the Edge Component run under SELinux.

Note

The rules included in the Edge Component installer packages are configured to support common workloads with default installation parameters. We recommend running Edge Components for 1-2 days in permissive mode in case SELinux raises violations due to custom configurations or unexpected workload interactions. See the [policy update section](#selinux-policy-updates) to learn how to update your Edge Component policy.

## SELinux policy updates

[Section titled “SELinux policy updates”](#selinux-policy-updates)

SELinux may report violations if an Edge Component is run with non-default installation options or with unique workloads. If this occurs, follow these steps to update the SELinux policy and allow the Edge Component to access the needed resources.

Note

Aembit recommends running SELinux in permissive mode while performing the following steps.

Note

The following steps use Agent Proxy as the example application. If you’re updating a policy for Agent Controller, replace occurrences of `proxy` with `controller` in script and/or directory names.

1. Change to the directory where you initially generated the SELinux policy files for your Edge Component (if you followed along from the [previous section](#create-an-selinux-policy), this was `~/edge_component_policy`).

   ```shell
   cd ~/edge_component_policy
   ```

2. Update the rules (`.te`) file to account for new violations by running the previously generated installation script with the `--update` flag.

   ```shell
   sudo ./aembit_agent_proxy.sh --update
   ```

3. Restart the Edge Component for the policy updates to take effect.

   ```shell
   sudo systemctl restart aembit_agent_proxy
   ```

Note

It can be useful in some situations to check for violations without committing to a policy update. You can do this with the `ausearch` tool:

```shell
# get the last time at which the policy was updated
last_update_time=`ls -l --time-style="+%x %T" aembit_agent_proxy.te | awk '{ printf "%s %s", $6, $7 }'`


# query SELinux for violations
ausearch --start $last_update_time -m avc --raw -se aembit_agent_proxy
```

## Edge Component version upgrades

[Section titled “Edge Component version upgrades”](#edge-component-version-upgrades)

When installing a new version of an Edge Component that’s monitored by SELinux, you may choose to re-use your existing rules (`.te`) file from a previous policy installation, or you can install a new policy from scratch using the `.te` file provided in the Edge Component’s installation bundle. Both options lead to a fully functioning SELinux policy.

* To create a new policy using the rules (`.te`) file provided in the new Edge Component’s installer bundle, follow the steps outlined in the [policy creation](#create-an-selinux-policy) section.

* To create a new policy using your existing rules (`.te`) file, follow the steps in the [policy creation](#create-an-selinux-policy) section, but use your previous `.te` file instead of the supplied one in the Edge Component’s installation bundle.
