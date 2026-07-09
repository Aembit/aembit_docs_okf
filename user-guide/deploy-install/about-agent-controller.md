---
type: how-to
title: "About the Aembit Agent Controller"
description: "Understanding the Agent Controller's role as a critical Edge component that facilitates secure registration and communication between Agent Proxies and Aembit Cloud"
resource: https://docs.aembit.io/user-guide/deploy-install/about-agent-controller/
interface: web-ui
tags: [deploy-install]
timestamp: 2026-06-27T20:47:56-07:00
type_inferred: true
---

# About the Aembit Agent Controller

Aembit’s **Agent Controller** is a critical [Aembit Edge](../../get-started/concepts/aembit-edge.md) component that serves as the registration broker for other Edge Components within your operational environments. It acts as the trusted intermediary that enables Agent Proxies to securely register with Aembit Cloud and obtain the credentials needed for [Access Policy](../../get-started/concepts/access-policies.md) enforcement.

Agent Controller simplifies the deployment and management of Aembit Edge by centralizing the registration process and providing a secure communication channel between your distributed Edge components and Aembit Cloud.

## Deployment options

[Section titled “Deployment options”](#deployment-options)

Agent Controller supports deployment across diverse computing environments to meet your infrastructure requirements:

### Virtual machines

[Section titled “Virtual machines”](#virtual-machines)

Deploy Agent Controller on dedicated virtual machines using native installers:

![](https://docs.aembit.io/3p-logos/linux-icon.svg)

[Linux virtual machines ](virtual-machine/linux/overview.md)Ubuntu and Red Hat Enterprise Linux

→

![](https://docs.aembit.io/3p-logos/windows-icon.svg)

[Windows virtual machines ](virtual-machine/windows/overview.md)Windows Server

→

### Container environments

[Section titled “Container environments”](#container-environments)

Deploy Agent Controller within containerized environments:

![](https://docs.aembit.io/3p-logos/kubernetes-icon.svg)

[Kubernetes clusters ](kubernetes/overview.md)Deployed via Helm charts with automatic configuration

→

![](https://docs.aembit.io/3p-logos/aws-ecs-icon.svg)

[AWS ECS Fargate ](serverless/aws-ecs-fargate.md)Container-based deployment using Terraform modules

→

### Specialized deployments

[Section titled “Specialized deployments”](#specialized-deployments)

Support for specialized deployment scenarios:

![](https://docs.aembit.io/3p-logos/aws-lambda-icon.svg)

[AWS Lambda deployments ](serverless/aws-lambda-function.md)Supports Edge component deployment in AWS Lambda

→

![](https://docs.aembit.io/aembit-icons/sliders-solid.svg)

[High availability configurations ](advanced-options/agent-controller/agent-controller-high-availability.md)Multiple instances with load balancing for production resilience

→

### Deployments without Agent Controller

[Section titled “Deployments without Agent Controller”](#deployments-without-agent-controller)

In some deployment models, like [Aembit CLI](../../cli-guide/usage/overview.md) for CI/CD and when your applications use the Aembit API directly, you may not need an Agent Controller, reducing operational complexity.

For more details, see [Aembit Edge on CI/CD services](ci-cd/overview.md).

## Key responsibilities

[Section titled “Key responsibilities”](#key-responsibilities)

Agent Controller performs multiple critical functions within the Aembit Edge architecture:

* **Controller Self-Registration** - The Agent Controller manages its own registration and attestation with Aembit Cloud to establish a foundational trust relationship for the environment it represents.

* **Token Provisioning** - Once registered, Agent Controller provides authentication tokens to Agent Proxies. The controller handles local token distribution, while Aembit Cloud centralizes the actual token management.

* **Trust establishment** - Establishes and maintains trust relationships between your environment and Aembit Cloud. Validates identity evidence from Trust Providers to ensure only authorized components can participate in the Aembit ecosystem.

* **Secure communication** - Manages TLS communication between Agent Proxies and itself, providing encrypted channels for sensitive registration and authentication data.

## How Agent Controller works

[Section titled “How Agent Controller works”](#how-agent-controller-works)

Agent Controller operates as part of the broader Aembit Edge registration and credential injection workflow:

### During registration

[Section titled “During registration”](#during-registration)

Agent Controller supports the following registration methods, each with its own workflow:

* Trust Provider-based

  Agent Controller uses [Trust Providers](../../get-started/concepts/trust-providers.md) which automate identity attestation through cloud provider metadata services or other trusted systems in your environment. Ideal for production and high-availability deployments.

  1. **Agent Controller attestation** - Agent Controller retrieves an attestation document from its local environment. Trust Providers exist in Aembit Cloud and can verify that Agent Controller has provided an attestation document that matches the Trust Provider configured for that Agent Controller.
  2. **Agent Controller registration** - Using the attestation, Agent Controller obtains an access token from Aembit Cloud and completes its secure registration
  3. **Agent Proxy token flow** - Agent Proxies request tokens from Agent Controller which obtains them from Aembit Cloud on their behalf
  4. **Agent Proxy registration** - Using the token provided by Agent Controller, Agent Proxies register with Aembit Cloud and establish their secure connection
  5. **Health reporting** - Agent Controller periodically sends health reports to Aembit Cloud

  ![Agent Controller Trust Provider-based registration sequence](https://docs.aembit.io/d2/docs/user-guide/deploy-install/about-agent-controller-0.svg)

* Device Code-based

  Device Codes are temporary one-time-use codes, valid for 15 minutes, that you use during installation to authenticate the Agent Controller with your Aembit Tenant.

  Device Codes vs Trust Providers

  Device Codes are a fallback authentication method for the Agent Controller, and they’re less secure than Trust Providers. Prefer a Trust Provider for production deployments, since Trust Providers offer stronger, automated attestation with better control and flexibility.

  Use a Device Code only in these cases:

  * A test, proof-of-concept, lab, or demo environment.
  * A production environment that has no Trust-Provider-based attestation available, such as a bare-metal or on-premises host, or VMware vSphere without cloud metadata services. In these environments, Device Code is the supported Agent Controller registration method.

  See [About the Aembit Agent Controller](about-agent-controller.md) for more information.

  1. **Device code flow** - Agent Controller requests a device code from Aembit Cloud and polls for an access token
  2. **Agent Controller registration** - Using the access token, Agent Controller completes its secure registration with Aembit Cloud
  3. **Agent Proxy token flow** - Agent Proxies request tokens from Agent Controller, which obtains them from Aembit Cloud on their behalf
  4. **Agent Proxy registration** - Using the token provided by Agent Controller, Agent Proxies register with Aembit Cloud and establish their secure connection
  5. **Health reporting** - Agent Controller periodically sends health reports to Aembit Cloud

  ![Agent Controller Device Code-based registration sequence](https://docs.aembit.io/d2/docs/user-guide/deploy-install/about-agent-controller-1.svg)

### During operation

[Section titled “During operation”](#during-operation)

Once registered, Agent Controller plays a continuous, active role in your Aembit Edge deployment. Its main operational responsibilities include:

1. **Token Management and Refresh**

   * **Proxy Token Requests** - Agent Proxies periodically request new access tokens from Agent Controller. This ensures that Agent Proxies always have valid credentials to interact with Aembit Cloud.
   * **Token Refresh** - Agent Controller securely stores refresh tokens and uses them to obtain new access tokens from Aembit Cloud as needed, without requiring re-registration.

2. **Health Reporting**

   * **Periodic Health Checks** - Agent Controller sends a health report to Aembit Cloud every minute over a secure connection. This report includes status, version, and uptime, enabling monitoring in your Aembit Tenant UI.
   * **Status Updates** - The Aembit Tenant UI displays the current health of each Agent Controller, including connection status and last reported uptime.

3. **TLS Certificate Reporting**
   * **Certificate Status** - If you enable TLS, Agent Controller reports its certificate status to Aembit Cloud. The Aembit Tenant UI displays certificate health, including expiration warnings.

4. **Metrics and Observability**
   * **Metrics** - Agent Controller provides Prometheus-compatible metrics, allowing integration with monitoring tools for timely observability of operational health, request rates, and resource usage.

![Agent Controller operational workflow including token refresh, health reporting, and TLS management](https://docs.aembit.io/d2/docs/user-guide/deploy-install/about-agent-controller-2.svg)

## Monitoring and health

[Section titled “Monitoring and health”](#monitoring-and-health)

Agent Controller provides robust monitoring and health reporting features to help you maintain operational visibility and ensure reliability in your Edge deployments.

### Where to find Agent Controller logs

[Section titled “Where to find Agent Controller logs”](#where-to-find-agent-controller-logs)

Agent Controller logs are essential for monitoring its operation and troubleshooting issues. The log file locations vary based on the operating system:

* Linux

  On VM deployments the logs should be available with the command:

  ```shell
  journalctl -n aembit_agent_controller
  ```

  This is the primary location for all Agent Controller service activity logs on Linux.

* Windows

  Agent Controller writes logs to:

  ```plaintext
  C:\ProgramData\Aembit\AgentController\Logs
  ```

  This is the primary location for all Agent Controller service activity logs on Windows. Logs aren’t removed on uninstall.

### What `ReportHealth` logs look like

[Section titled “What ReportHealth logs look like”](#what-reporthealth-logs-look-like)

When Agent Controller sends a health report to Aembit Cloud, you’ll see log entries like:

**On Success**:

```plaintext
Cloud Health Reporting Service sent the Health Report to the Cloud successfully.
```

**On Failure**:

```plaintext
Error while getting Report Health from gRPC
```

Tip

If you’re troubleshooting health reporting, look for these log entries in the Agent Controller logs. A successful message means Agent Controller is reporting health status to Aembit Cloud; errors indicate connectivity or configuration issues.

### Health reporting

[Section titled “Health reporting”](#health-reporting)

**Automatic Health Checks** - Agent Controller sends a health report to Aembit Cloud every minute over a secure connection. This report includes the controller’s status, version, and uptime.

**Status Indicators in your Aembit Tenant UI**

* **Healthy** - Displayed as a green dot in the Aembit Tenant UI if Agent Controller sends a healthy status to Aembit Cloud within the last 90 seconds.
* **Disconnected** - If Agent Controller reports no healthy status within 90 seconds, a disconnected icon appears.
* **Last Reported Uptime** - Hovering over the status icon shows the last reported uptime for the Agent Controller.

**Health States**

* **Healthy** - Registered and connected to Aembit Cloud.
* **Registered** - Registered but not fully healthy (for example, waiting for additional attestation).
* **Unregistered** - Not registered with device code or trust provider.
* **RegisteredAndNotConnected** - Registered, but the connection to Aembit Cloud is down.

![Administration - Agent Controller UI statuses](https://docs.aembit.io/_astro/admin-agent-controller-statuses.BiOPMxvR_1mDFL9.webp)

### TLS status

[Section titled “TLS status”](#tls-status)

The **TLS** column in the Agent Controller list provides an at-a-glance view of each controller’s TLS certificate status for Agent Controller communication with Agent Proxies. This helps identify expiring or misconfigured certificates.

Note

This TLS status refers to Agent Controller’s own certificates for secure communication with Agent Proxies and Aembit Cloud. This is separate from TLS Decrypt certificates, which Agent Proxy manages for decrypting application traffic. For TLS Decrypt certificate monitoring, see [Configure TLS Decrypt](advanced-options/tls-decrypt/configure-tls-decrypt.md).

The **TLS** status uses color-coded icons (and sometimes tooltips) to show the health of the Agent Controller’s TLS certificate:

* **Green**: More than 30 days until certificate expiration.
* **Yellow**: Certificate expires within 30 days.
* **Red**: Certificate expires within 7 days or is already expired.
* **Blue**: (For Aembit-managed TLS) Indicates the certificate is valid, managed by Aembit, and automatically rotates them.
* **Grey/Not configured**: TLS isn’t configured for this Agent Controller.

### Metrics and observability

[Section titled “Metrics and observability”](#metrics-and-observability)

Agent Controller exposes operational metrics to help you monitor performance and health:

* **Key metrics tracked** include:

  * Request rates (for example, token issuance, registration)
  * Latency and error rates
  * Resource utilization (CPU, memory)
  * Active connections and uptime

* **Prometheus-compatible metrics** - Agent Controller provides operational metrics in Prometheus format. This enables integration with observability platforms for rapid monitoring and alerting.\
  See [Aembit Edge Prometheus-compatible metrics](advanced-options/aembit-edge-prometheus-compatible-metrics.md) for details.

## High availability considerations

[Section titled “High availability considerations”](#high-availability-considerations)

For production deployments, configure Agent Controller in a [high availability setup](advanced-options/agent-controller/agent-controller-high-availability.md):

* **Redundancy** - Multiple Agent Controller instances remove single points of failure.
* **Load Balancing** - TCP load balancers distribute traffic across healthy instances.
* **Health Monitoring** - Automated health checks detect failures and trigger remediation.
* **TLS Management** - Proper certificate configuration for load-balanced environments.

## Security features and best practices

[Section titled “Security features and best practices”](#security-features-and-best-practices)

Agent Controller incorporates multiple security mechanisms:

### TLS encryption

[Section titled “TLS encryption”](#tls-encryption)

Agent Controller supports both Aembit-managed and customer-managed PKI for securing communication between itself and Agent Proxies:

* [Aembit PKI configuration](advanced-options/agent-controller/configure-aembit-pki-agent-controller-tls.md) - Default option for ease of use managed by Aembit
* [Customer PKI configuration](advanced-options/agent-controller/configure-customer-pki-agent-controller-tls.md) - For organizations with existing PKI infrastructure

### Identity validation

[Section titled “Identity validation”](#identity-validation)

Agent Controller may use Trust Providers to authenticate itself with Aembit Cloud, enabling it to provide tokens for the deployment.

Agent Controller supports a limited set of Trust Providers for authentication:

* AWS IAM Roles and EC2 Instance Identity
* Azure Managed Identity
* Google Cloud Service Accounts

See the Aembit Support Matrix [Agent Controller Trust Providers](../../reference/support-matrix.md) section for details.

Note

This only applies when using Trust Provider-based authentication. As a best practice, use Trust Provider-based registration in production environments. Use Device Code-based registration for testing or proof-of-concept deployments. In production, use Device Code only where no Trust Provider-based attestation is available, such as bare-metal, on-premises, or VMware vSphere without cloud metadata.

See [During registration](#during-registration) for more details.

## Integration with the Aembit ecosystem

[Section titled “Integration with the Aembit ecosystem”](#integration-with-the-aembit-ecosystem)

Agent Controller is a core part of the Aembit Edge architecture, acting as the bridge between distributed Edge components and the Aembit Cloud control plane. It enables secure registration, policy retrieval, and health monitoring across your environment.

### Related topics

[Section titled “Related topics”](#related-topics)

* **[About TLS Decrypt](advanced-options/tls-decrypt/overview.md)** - Learn how Agent Proxy performs TLS decryption with Agent Controller support
* **[Agent Proxy installation](virtual-machine/linux/agent-proxy-install-linux.md)** - Install the component that performs TLS decryption
* **[Trust Providers](../../get-started/concepts/trust-providers.md)** - Identity attestation for secure registration
* **[Aembit Edge](../../get-started/concepts/aembit-edge.md)** - Overview of Aembit’s Edge architecture
* **[Aembit Cloud](../../get-started/concepts/aembit-cloud.md)** - Overview of Aembit’s Cloud control plane
