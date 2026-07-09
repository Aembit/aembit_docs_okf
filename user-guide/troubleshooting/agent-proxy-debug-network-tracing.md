---
type: troubleshooting
title: "Agent Proxy Debug Network Tracing"
description: "This page describes how you can utilize the Agent Proxy Debug Network Tracing feature to capture and record network traffic in a Virtual Machine deployment."
resource: https://docs.aembit.io/user-guide/troubleshooting/agent-proxy-debug-network-tracing/
interface: web-ui
tags: [troubleshooting]
timestamp: 2025-05-29T11:26:12-07:00
type_inferred: true
---

# Agent Proxy Debug Network Tracing


Agent Proxy has the ability to capture a rolling window of the most recent network traffic on your host’s network devices, a feature referred to as Debug Network Tracing. When enabled, Agent Proxy:

* writes a package capture file (`.pcap`) to the local disk whenever it encounters certain errors (currently limited to TLS “certificate unknown” occurrences).

* writes a `.pcap` file with the most recent network packets for all devices when receiving `POST /write-pcap-file` on the HTTP service server endpoint (defaults to `localhost:51234` unless configured otherwise).

With this information, you can review network traffic information to locate the error and perform remediation steps to resolve the issue.

Note

Debug Network Tracing is “off by default; therefore, you must enable this feature directly.

## Configuring Debug Network Tracing for Agent Proxy

[Section titled “Configuring Debug Network Tracing for Agent Proxy”](#configuring-debug-network-tracing-for-agent-proxy)

Configuring Agent Proxy to capture network traffic information requires you to perform the steps listed below.

1. Go to the [Virtual Machine installation](../deploy-install/virtual-machine/overview.md) page in the Aembit technical documentation.

2. Follow the steps described in the [Agent Proxy Installation](../deploy-install/virtual-machine/linux/agent-proxy-install-linux.md) section to install Agent Proxy.

3. When installing Agent Proxy, supply the following environment variable to the Agent Proxy VM installer:

`AEMBIT_DEBUG_MAX_CAPTURED_PACKETS_PER_DEVICE=<N>`

* Where `N` is the number of packets you would like to have Agent Proxy capture, while also determining the size of the rolling window. For example, if you set `N` to `2000`, this means that Agent Proxy will monitor and keep a history of the last 2000 network packets for each IPv4 device.

Your command should look like the example shown below.

```shell
sudo AEMBIT_AGENT_CONTROLLER=http://<AGENT CONTROLLER HOST>:5000 AEMBIT_DEBUG_MAX_CAPTURED_PACKETS_PER_DEVICE=2000 [...] ./install
```

4. Agent Proxy debug network tracing is now enabled, and you are able to review network traffic on your devices.
