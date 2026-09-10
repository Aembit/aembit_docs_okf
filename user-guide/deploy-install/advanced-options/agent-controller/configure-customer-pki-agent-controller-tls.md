---
type: how-to
title: "Configure a custom PKI-based Agent Controller TLS"
description: "How to configure a custom PKI-based Agent Controller TLS in Kubernetes and Virtual Machine deployments"
resource: https://docs.aembit.io/user-guide/deploy-install/advanced-options/agent-controller/configure-customer-pki-agent-controller-tls/
interface: web-ui
tags: ["agent-controller", "advanced-option", "deploy-install"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Configure a custom PKI-based Agent Controller TLS

Aembit provides the ability for you to use your own PKI-based TLS for secure Agent Proxy to Agent Controller communication in Kubernetes environments, and on Virtual Machine deployments.

> **Note**
>
> When you enable TLS on Agent Controller version 1.24.xxxx or later, Agent Controller automatically:
>
> * opens port 443 (5443 on VM) for HTTPS traffic
>
> * doesn’t open port 80 (5000 on VM) for HTTP traffic
>
> As of Agent Controller version 1.29.xxxx, on Kubernetes deployments, Agent Controller exposes Prometheus metrics over HTTP on port 9090 regardless of TLS state. On Virtual Machine deployments, enabling TLS continues to switch Prometheus metrics from HTTP port 9090 to HTTPS port 9091.

## Prerequisites

* Access to a Certificate Authority such as HashiCorp Vault or Microsoft Active Directory Certification Authority.

* A TLS PEM Certificate and Key file pair you’ve configured for the hostname of the Agent Controller.

  * On Kubernetes, the hostname must be `aembit-agent-controller.<namespace>.svc.cluster.local` where `<namespace>` is the namespace where you installed the Aembit Helm chart.
  * On Virtual Machines, the hostname is going to depend on your network and DNS configuration. Please use the FQDN or PQDN hostname which Agent Proxy instances use to access the Agent Controller.

* The TLS PEM Certificate file should contain both the Agent Controller certificate and chain to the Root CA.

* Self-signed certificates aren’t supported by the Agent Proxy for Agent Controller TLS communication.

## Kubernetes environment configuration

The Aembit Agent Controller requires that the TLS certificate and key be available in a [Kubernetes TLS Secret](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_create/kubectl_create_secret_tls/). Therefore, there are 2 steps to completing this configuration.

1. Create a Kubernetes TLS Secret using the `kubectl create secret tls` command or similar method. For example:

   ```shell
   kubectl create secret tls NAME --cert=path/to/cert/file --key=path/to/key/file
   ```

2. In the Aembit Helm chart installation file, set the `agentController.tls.secretName` value equal to the name of the secret created in step #1.

   > **Note**
   >
   > Both prior steps assume that the TLS Secret and Aembit Helm chart are installed into the same namespace.

If you don’t have your own CA, you may consider [Kubernetes cert-manager](https://github.com/cert-manager/cert-manager) to create and maintain certificates and keys in your Kubernetes environment.

## Virtual machine environment configuration

When installing the Agent Controller on a Virtual Machine, there are two installation parameters that must be specified:

* `TLS_PEM_PATH`
* `TLS_KEY_PATH`

For example, the Agent Controller installation command line could be specified like:

```shell
sudo TLS_PEM_PATH=/path/to/tls.crt TLS_KEY_PATH=/path/to/tls.key AEMBIT_TENANT_ID=tenant AEMBIT_AGENT_CONTROLLER_ID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee ./install
```

## Rotating custom PKI Agent Controller TLS certificates

Regular certificate rotation is essential to ensure that certificates remain valid and only expire when you expect them to. By routinely updating certificates before their expiration, you prevent service disruptions and maintain secure communication.

In the Aembit environment, Agent Controller stores TLS certificate and key files in the `/opt/aembit/edge/agent_controller` directory.

### Update TLS certificate

To update your TLS certificate and key, perform these steps:

1. Replace the existing TLS certificate and key files in the `/opt/aembit/edge/agent_controller` directory with the new key files provided by the customer.

2. Ensure the ownership of these new files matches the original permissions (`user: aembit_agent_controller, group aembit`).

   ```shell
   sudo chown aembit_agent_controller:aembit /opt/aembit/edge/agent_controller/tls.crt
   sudo chown aembit_agent_controller:aembit /opt/aembit/edge/agent_controller/tls.key
   ```

3. Verify the file permissions match the original settings.

   ```shell
   $: /opt/aembit/edge/agent_controller# ls -l
   -r-------- 1 aembit_agent_controller aembit ....... tls.crt
   -r-------- 1 aembit_agent_controller aembit ....... tls.key
   ```

4. After you have replaced the files and adjusted the permissions, restart the Agent Controller service to apply these changes.

   ```shell
   sudo systemctl restart aembit_agent_controller
   ```

5. You can verify that TLS certificate/key was successfully rotated by checking for the following log message:

   ```shell
   $: journalctl --namespace aembit_agent_controller | grep "Tls certificate sync background process"
   [INF] (Aembit.AgentController.Business.Services.BackgroundServices.TlsSyncUpService)
   ```

   * If you’ve configured TLS successfully, you’ll see the following message:

     ```shell
     Tls certificate sync background process is active.
     ```

   * If TLS isn’t configured successfully, you’ll’ see the following message displayed:

     ```shell
     Tls certificate sync background process will not run because Tls is not enabled.
     ```
