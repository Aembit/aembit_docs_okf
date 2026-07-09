---
type: explanation
title: "Deploying Aembit Edge on VMs"
description: "Guides and topics about deploying Aembit Edge Components on virtual machines (VMs)"
resource: https://docs.aembit.io/user-guide/deploy-install/virtual-machine/
tags: [virtual-machine, deploy-install]
timestamp: 2025-05-29T11:26:12-07:00
type_inferred: true
---

# Deploying Aembit Edge on VMs

You can run Aembit Edge Components on virtual machines (VMs) to enable secure, identity-based access between workloads.

When deploying on VMs, you install Agent Controller and Agent Proxy directly onto each machine.

After installation, you must register Agent Proxy with an Agent Controller configured with a [Trust Provider](../../access-policies/trust-providers/overview.md) or with your Aembit Tenant using a one-time Device Code.

Once deployed, the Agent Proxy intercepts workload traffic, injects credentials, and enforces access policies—without requiring application changes.

This section provides installation guides for deploying Aembit Edge Components on VMs in Linux and Windows environments.

Note

Aembit recommends deploying Agent Controller and Agent Proxy on standalone VMs and not collocating them on the same VM.

See [About Colocating Aembit Edge Components](../about-colocating-edge-components.md) for more info.

## By operating system

[Section titled “By operating system”](#by-operating-system)

The following sections provide installation guides by Linux and Windows operating systems:

### Linux installation guides

[Section titled “Linux installation guides”](#linux-installation-guides)

* [Agent Controller](linux/agent-controller-install-linux.md)
* [Agent Proxy](linux/agent-proxy-install-linux.md)
* [Agent Proxy on SELinux or RHEL](linux/agent-proxy-selinux-config.md)

### Windows installation guides

[Section titled “Windows installation guides”](#windows-installation-guides)

* [Agent Controller](windows/agent-controller-install-windows.md)
* [Agent Proxy](windows/agent-proxy-install-windows.md)

## By Edge Component

[Section titled “By Edge Component”](#by-edge-component)

The following sections provide installation guides by Aembit Edge Components

### Agent Controller

[Section titled “Agent Controller”](#agent-controller)

* [Linux](linux/agent-controller-install-linux.md)
* [Windows](windows/agent-controller-install-windows.md)

### Agent Proxy

[Section titled “Agent Proxy”](#agent-proxy)

* [Linux](linux/agent-proxy-install-linux.md)
* [SELinux or RHEL](linux/agent-proxy-selinux-config.md)
* [Windows](windows/agent-proxy-install-windows.md)
