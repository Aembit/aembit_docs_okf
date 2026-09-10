---
type: explanation
title: "Agent Controller High Availability"
description: "How to install and configure Agent Controllers in a high availability configuration"
resource: https://docs.aembit.io/user-guide/deploy-install/advanced-options/agent-controller/agent-controller-high-availability/
tags: ["agent-controller", "advanced-option", "deploy-install"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Agent Controller High Availability

The Agent Controller is a critical Aembit Edge Component that facilitates Agent Proxy registration. Ensuring the continuous availability of the Agent Controller is vital for the uninterrupted operation of Agent Proxies. As a result, for any production deployment, it’s essential to install and configure the Agent Controller in a high availability configuration.

[Three key principles](https://en.wikipedia.org/wiki/High_availability#Principles) must be addressed to achieve high availability for the Agent Controller:

* Elimination of single points of failure

* Ensuring reliable crossover

* Failure detection

## Remove single points of failure

Having one Agent Controller instance can be a single point of failure. To mitigate this, multiple Agent Controller instances should be operational within an environment, providing redundancy and eliminating this risk.

To deploy multiple instances, repeat the [Agent Controller installation procedure](../../virtual-machine/overview.md).

Trust Provider-based registration of the Agent Controller simplifies launching multiple instances, as it removes the need to generate a new device code for each instance. When employing this method, you can use the same Agent Controller ID while installing additional instances for the same logical Agent Controller.

If you opt for the device code registration method, you must create a separate Agent Controller entry for each deployed instance in your tenant.

## Ensure reliable crossover

For effective traffic routing to multiple Agent Controller instances, use a load balancer.

It’s critical that the load balancer itself is configured for high availability to avoid becoming a single point of failure.

To accommodate the technical requirement of load balancing HTTPS (encrypted) traffic between Agent Proxies and Agent Controllers, a TCP load balancer (Layer 4) is necessary. Choose a TCP load balancer that aligns with your company’s preferences and standards.

## Failure detection

Monitoring of both Agent Controllers and load balancers is necessary to quickly detect any failures. Establish a manual or automated procedure for failure remediation upon detection.

The health status of an Agent Controller can be checked through an `HTTP GET` request to the /health endpoint on port 80. A healthy Agent Controller will return an HTTP Response code of `200`.

## Transport Layer Security (TLS)

When Transport Layer Security (TLS) is configured on Agent Controllers behind a load balancer, it is crucial for the certificates on these Agent Controllers to include the domain names associated with the load balancer. This ensures that SSL/TLS termination at the Agent Controllers presents a certificate valid for the domain names clients use to connect.

### Agent Controller health endpoint Swagger documentation

```yaml
openapi: 3.0.0
info:
  title: Agent Controller Health Check API
  version: 1.0.0
paths:
  /health:
    get:
      summary: Agent Controller Health Check Endpoint
      description: Returns the health status of the Agent Controller.
      responses:
        '200':
          description: Healthy - the Agent Controller is functioning properly.
          content:
            application/json:
              schema:
                type: object
                properties:
                  status:
                    type: string
                    example: "Healthy"
                  version:
                    type: string
                    example: "1.9.696"
                  gitSHA:
                    type: string
                    example: "b16139605d32ce60db0a5682de8ee3b579c6e885"
                  host:
                    type: string
                    example: "hostname"
        '401':
          description: Unhealthy - the Agent Controller is not registered yet or can't register.
          content:
            application/json:
              schema:
                type: object
                properties:
                  status:
                    type: string
                    example: "Unregistered"
                  version:
                    type: string
                    example: "1.9.696"
                  gitSHA:
                    type: string
                    example: "b16139605d32ce60db0a5682de8ee3b579c6e885"
                  host:
                    type: string
                    example: "hostname"
```

> **Note**
>
> A newly deployed Agent Controller may take up to 10 seconds to register and attain a healthy state.
