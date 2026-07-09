---
type: how-to
title: "Virtual Appliance"
description: "This page describes the steps required to deploy the Aembit Edge Components as a virtual appliance."
resource: https://docs.aembit.io/user-guide/deploy-install/virtual-appliances/virtual-appliance/
interface: web-ui
tags: [virtual-appliance, deploy-install]
timestamp: 2025-05-29T11:26:12-07:00
type_inferred: true
---

# Virtual Appliance

Note

This feature is available as a limited beta only. Please contact your Aembit representative for more information.


The Aembit Edge Components can be deployed as a virtual appliance. This allows more than one Client Workload to use the same set of Edge Components. Aembit provides an OVA file suitable for deployment on a [VMWare ESXi](https://www.vmware.com/products/cloud-infrastructure/esxi-and-esx) server.

## Limitations

[Section titled “Limitations”](#limitations)

The virtual appliance deployment model is limited in the following ways:

1. Only explicit steering is supported.
2. Only HTTP(S) and Snowflake traffic is supported.
3. Client Workloads may only be identified by the source IP.
4. There are no Trust Providers currently compatible.
5. Of the current Access Conditions, only the **Aembit Time Condition** is compatible.

## Deployment Instructions

[Section titled “Deployment Instructions”](#deployment-instructions)

For VM-creation details for your specific ESXi version, consult the [vSphere Documentation](https://docs.vmware.com/en/VMware-vSphere/index.html).

1. Download the virtual appliance OVA from the [Virtual Appliance Releases](https://releases.aembit.io/edge_virtual_appliance/index.html).
2. Upload the OVA to your ESXi server.
3. Create a new virtual machine, entering the appropriate configuration values. See the below [Configurations](#configurations) section for details.
4. Deploy the virtual machine.
5. Log into the virtual machine. For login details, please contact your Aembit representative.

Danger

Immediately update the `aembit_edge` user password using by running the `passwd` command and supplying a new password.

### Device Code Expiration

[Section titled “Device Code Expiration”](#device-code-expiration)

In the event your device code expires before installation is complete, please contact your Aembit representative for assistance.

## Configurations

[Section titled “Configurations”](#configurations)

There are two fields that must first be populated for a virtual appliance deployment to succeed:

1. `AEMBIT_TENANT_ID`
2. `AEMBIT_DEVICE_CODE`

The virtual appliance deployment uses a subset of the virtual machine deployment options. See the [virtual machine deployment](../virtual-machine/overview.md) page for a detailed discussion of these options.

## Usage

[Section titled “Usage”](#usage)

Configure the proxy configuration of your Client Workloads to send traffic to the virtual appliance. For more information on configuring the proxy settings of your Client Workload, see [Explicit Steering](../advanced-options/agent-proxy/explicit-steering.md#configure-explicit-steering).
