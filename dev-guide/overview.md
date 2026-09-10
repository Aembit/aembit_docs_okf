---
type: reference
title: "Developer Guide"
description: "Every path for integrating with Aembit. Choose how your workload gets credentials at runtime and how you manage configuration."
resource: https://docs.aembit.io/dev-guide/
timestamp: 2026-09-08T23:32:41-07:00
---

# Developer Guide

Integrating with Aembit involves two separate decisions. First, choose how your workload gets credentials at runtime: through infrastructure that intercepts its traffic, or through code or commands that request them explicitly. Second, choose how you manage the Aembit configuration behind every path: in the Aembit Tenant UI, through the Cloud API, or as Terraform code. This page maps all the paths so you can pick the combination that matches how you build.

## Explore the developer surfaces

![Aembit Edge Icon](https://docs.aembit.io/aembit-icons/aembit-edge.svg)

[Agent Proxy](integration/agent-proxy.md)Integrate with no code changes. Agent Proxy intercepts your workload's traffic and injects credentials in transit.

→

![Code Icon](https://docs.aembit.io/aembit-icons/gear-complex-code-solid.svg)

[SDKs](sdk/edge/overview.md)Embed Aembit in your application. Language libraries that authenticate workloads and retrieve credentials without hand-rolling HTTP.

→

![Cloud Icon](https://docs.aembit.io/aembit-icons/cloud-solid.svg)

[API](api/overview.md)Call Aembit directly over REST. The Edge API for runtime credential retrieval and the Cloud API for managing your Aembit resources.

→

![Terminal Icon](https://docs.aembit.io/aembit-icons/code-solid.svg)

[CLI](cli/overview.md)Get credentials from your terminal. A command-line tool for scripts, CI/CD jobs, and ops automation.

→

## The two planes

Aembit divides its work between two planes. Both planes run in Aembit Cloud. The **management plane** is where people and automation define the configuration. They write it through the UI of your Aembit Tenant, the Cloud API, or the Terraform provider. That configuration is the Access Policies, each binding a Client Workload, Trust Provider, Credential Provider, and Server Workload. The **control plane** authorizes workload access at runtime: it verifies the workload’s identity, evaluates the Access Policy, and brokers the credential. Nobody works in the control plane directly: Aembit Edge components and the Edge SDK, Edge API, and Aembit CLI talk to it on a workload’s behalf. No workload gets a credential through the management plane, and no one configures Access Policies through the control plane.

| Plane            | Who works there                                                | Interfaces                               | Responsibility                                                                 |
| ---------------- | -------------------------------------------------------------- | ---------------------------------------- | ------------------------------------------------------------------------------ |
| Management plane | Administrators, security engineers, and platform automation    | Tenant UI, Cloud API, Terraform provider | Define and maintain the Access Policies and their components, and audit access |
| Control plane    | Workloads, through Aembit Edge components or the runtime paths | Agent Proxy, Edge SDK, Edge API, CLI     | Verify identity, evaluate the Access Policy, and broker the credential         |

For who does what in each plane and how the planes interact at runtime, see [Planes and responsibilities](../get-started/concepts/planes-and-responsibilities.md).

## Get credentials into your workload at runtime

Aembit gives you four ways to get a credential into a workload. They differ mainly in where the credential-handling logic lives: in infrastructure you deploy, or in code you write.

| Path                                                   | Runs as                                    | Application changes                          | Best for                                                                                                                        |
| ------------------------------------------------------ | ------------------------------------------ | -------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| **[Agent Proxy](integration/agent-proxy.md)** | A component you deploy in your environment | None, because it intercepts outbound traffic | Workloads you can’t modify, and long-running services on Kubernetes or virtual machines                                         |
| **[Edge SDK](sdk/edge/overview.md)**                   | A library inside your application process  | Your code calls the SDK                      | Applications, AI agents, MCP servers, serverless functions, and short-lived jobs where you own the source but can’t run a proxy |
| **[Aembit CLI](cli/overview.md)**                      | A command in your shell or pipeline        | None, because you invoke a command           | Scripts, CI/CD steps, and interactive terminal use                                                                              |
| **[Edge API](api/edge/overview.md)**                   | HTTP requests you write yourself           | Your code calls the API                      | Languages the SDKs don’t cover, or when you need direct control over the HTTP layer                                             |

These four paths retrieve credentials at runtime. The [Cloud API](api/cloud/overview.md) is the programmatic path for the other decision, creating and managing the configuration that authorizes them, which [Manage your Aembit configuration](#manage-your-aembit-configuration) covers.

### No development required

The Agent Proxy path involves no application code beyond [placeholder credentials](integration/client-library-patterns.md#understanding-placeholder-credentials), so a team can adopt Aembit entirely through the Tenant UI. Configure the Access Policy and its components there, [deploy Agent Proxy](../user-guide/deploy-install/overview.md), and leave the application unchanged.

### Explicit retrieval in code

The Edge SDK and Edge API paths put credential retrieval in your source. In-code retrieval suits environments where a proxy isn’t practical, such as serverless functions, ephemeral CI containers, and platforms where you don’t control the runtime. Choose the [Edge SDK](sdk/edge/overview.md) when a supported language library exists, because it handles workload attestation, token caching, and refresh for you. Drop to the [Edge API](api/edge/overview.md) when you need the raw HTTP layer or work in a language without an SDK.

### Credentials at the command line

The [Aembit CLI](cli/overview.md) retrieves credentials into environment variables, which fits scripts, CI/CD jobs, and [local development](integration/local-development.md).

### Continue with the integration pages

Whichever path you choose, the integration pages cover the work that follows:

* **[Integrate through Agent Proxy](integration/agent-proxy.md)** - The developer-side procedure, from placeholder credential to verified request
* **[Client library patterns for Agent Proxy](integration/client-library-patterns.md)** - Where the placeholder goes for OAuth SDKs, API key headers, and database drivers
* **[Local development](integration/local-development.md)** - Get credentials while developing on your own machine
* **[Test and debug your integration](integration/testing.md)** - Verify credential delivery end to end on any path

## Manage your Aembit configuration

Every runtime path depends on the same Aembit configuration. A Trust Provider verifies the workload’s identity, and a Credential Provider supplies the credential. An Access Policy authorizes a specific Client Workload to reach a specific Server Workload.

You can manage that configuration three ways:

| Path                                                                                        | Works as                                                                | Best for                                                                        |
| ------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------- | ------------------------------------------------------------------------------- |
| **[Aembit Tenant UI](../user-guide/overview.md)**                                                        | Configuration screens in your Tenant                                    | Interactive setup and day-to-day administration                                 |
| **[Cloud API](api/cloud/overview.md)**                                                      | REST endpoints for every Aembit resource, with an OpenAPI specification | Automation, custom tooling, and clients you generate in your own language       |
| **[Terraform provider](https://registry.terraform.io/providers/Aembit/aembit/latest/docs)** | Infrastructure-as-code resources such as `aembit_server_workload`       | Declarative, version-controlled configuration alongside your existing Terraform |

The [User Guide](../user-guide/overview.md) documents every component’s Tenant UI configuration screens, and the [Access Policies section](../user-guide/access-policies/overview.md) covers the Access Policy components the runtime paths depend on.

For example, the following Cloud API request and Terraform configuration each define the same Server Workload for a PostgreSQL database:

* Cloud API

  ```shell
  curl -X POST -L 'https://<tenant>.aembit.io/api/v1/server-workloads' \
    -H 'Authorization: Bearer <TOKEN>' \
    -H 'Content-Type: application/json' \
    -d '{
      "name": "Example PostgreSQL workload",
      "isActive": true,
      "resourceSet": "<resource-set-id>",
      "serviceEndpoint": {
        "host": "database.example.com",
        "port": 5432,
        "requestedPort": 5432,
        "appProtocol": "PostgreSQL",
        "transportProtocol": "TCP",
        "tls": true,
        "requestedTls": true,
        "tlsVerification": "full",
        "workloadServiceAuthentication": {
          "method": "Password Authentication",
          "scheme": "Password"
        }
      }
    }'
  ```

  The [Cloud API overview](api/cloud/overview.md) covers authentication and the request conventions, and the OpenAPI specification linked there documents every endpoint.

* Terraform

  **main.tf**

  ```hcl
  terraform {
    required_providers {
      aembit = {
        source = "aembit/aembit"
      }
    }
  }


  resource "aembit_server_workload" "postgres" {
    name      = "Example PostgreSQL workload"
    is_active = true


    service_endpoint = {
      host               = "database.example.com"
      port               = 5432
      app_protocol       = "PostgreSQL"
      transport_protocol = "TCP"
      tls                = true
      requested_tls      = true
      tls_verification   = "full"


      authentication_config = {
        method = "Password Authentication"
        scheme = "Password"
      }
    }
  }
  ```

  The [provider documentation](https://registry.terraform.io/providers/Aembit/aembit/latest/docs) covers authentication and every resource, and [Terraform configuration for Aembit](../user-guide/access-policies/advanced-options/terraform/terraform-configuration.md) covers tenant-side setup.
