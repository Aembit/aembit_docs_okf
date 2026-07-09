---
type: troubleshooting
title: "Agent Proxy Connectivity"
description: "This page describes steps for investigating and troubleshooting issues with Agent Proxy connectivity."
resource: https://docs.aembit.io/user-guide/troubleshooting/agent-proxy-connectivity/
interface: web-ui
tags: [troubleshooting]
timestamp: 2025-04-14T12:09:34-07:00
type_inferred: true
---

# Agent Proxy Connectivity

### Potential culprit

[Section titled “Potential culprit”](#potential-culprit)

If the Aembit Agent Proxy cannot establish a connection either to the Agent Controller or to the Aembit Cloud, Agent Proxy will not be able to receive directives and credentials from the Aembit Cloud.

If you do not see Workload events for your Client Workload and Server Workload pair, the issue with connectivity could be one of the potential culprits.

You will need to access the terminal of a Virtual Machine or a container where the Aembit Agent Proxy is running using your preferred method.

Please use your preferred method to access the terminal of a Virtual Machine or container where the Aembit Agent Proxy is running.

Note

If your Client Workload is running in Kubernetes, the Aembit Agent Proxy will be added as a sidecar to the Client Workload container, and you can access it by executing:

```shell
kubectl exec -it <Client Workload pod name> -c aembit-agent-proxy -- bash
```

### Troubleshooting steps

[Section titled “Troubleshooting steps”](#troubleshooting-steps)

The next step is to check connectivity to the Agent Controller and Aembit Cloud by executing these commands (If necessary, telnet needs to be installed):

```shell
telnet <Agent Controller Host> <Agent Controller port: 5000 or 5443>


telnet <Tenant Id>.aembit.io 443
```

If either DNS resolution or TCP connectivity fails, please check your DNS and firewall setup to allow the Aembit Agent Proxy to establish these connections.
