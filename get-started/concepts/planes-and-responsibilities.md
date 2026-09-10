---
type: explanation
title: "Planes and responsibilities"
description: "What the management plane and control plane each do, who works in each, and how they interact when a workload requests access"
resource: https://docs.aembit.io/get-started/concepts/planes-and-responsibilities/
tags: ["concept"]
timestamp: 2026-08-27T18:01:41-07:00
---

# Planes and responsibilities

Aembit divides its work between two planes, and each plane has its own users, interfaces, and responsibilities. Understanding the split tells you which part of the documentation applies to you. Administrators and automation work in the management plane, and your workloads interact with the control plane at runtime. This page explains what each plane does, who works in it, and how the two interact when a workload requests access.

## The two planes

The **management plane** is where people and automation define the Aembit configuration. That configuration is the Access Policies and the components each one binds. A Trust Provider verifies a workload’s identity. A Credential Provider supplies the credential. An Access Policy authorizes a specific Client Workload to reach a specific Server Workload.

Three interfaces write that same configuration: the [Tenant UI](../../user-guide/overview.md) of your Aembit Tenant, the [Cloud API](../../dev-guide/api/cloud/overview.md), and the [Terraform provider](https://registry.terraform.io/providers/Aembit/aembit/latest/docs), which is itself a Cloud API client.

The **control plane** authorizes workload access at runtime. When a workload requests access, the control plane verifies the workload’s identity through attestation, evaluates the Access Policy, and brokers the credential the workload needs. Nobody works in the control plane directly: Aembit Edge components and the [Edge SDK](../../dev-guide/sdk/edge/overview.md), [Edge API](../../dev-guide/api/edge/overview.md), and [Aembit CLI](../../dev-guide/cli/overview.md) talk to it on a workload’s behalf.

The two planes never trade jobs. No workload gets a credential through the management plane, and no one configures Access Policies through the control plane.

| Plane            | Who works there                                                | Interfaces                               | Responsibility                                                                 |
| ---------------- | -------------------------------------------------------------- | ---------------------------------------- | ------------------------------------------------------------------------------ |
| Management plane | Administrators, security engineers, and platform automation    | Tenant UI, Cloud API, Terraform provider | Define and maintain the Access Policies and their components, and audit access |
| Control plane    | Workloads, through Aembit Edge components or the runtime paths | Agent Proxy, Edge SDK, Edge API, CLI     | Verify identity, evaluate the Access Policy, and broker the credential         |

Both planes run in Aembit Cloud, and [About Aembit Cloud](aembit-cloud.md) covers the internal architecture of each.

## Who does what

**Administrators and security engineers** work in the management plane. They create the Access Policies and their components, decide which workloads may reach which services, and review authorization events. Their home in the documentation is the [User Guide](../../user-guide/overview.md).

**Platform and DevOps engineers** also work in the management plane, but through automation. They script configuration through the [Cloud API](../../dev-guide/api/cloud/overview.md) or declare it as [Terraform code](https://registry.terraform.io/providers/Aembit/aembit/latest/docs), so the same Access Policies and their components become repeatable and version-controlled.

**Developers** decide how their workload participates in the control plane at runtime. Their only decision is where the credential handling lives. That handling lives in infrastructure with [Agent Proxy](../../dev-guide/integration/agent-proxy.md), in code with the [Edge SDK](../../dev-guide/sdk/edge/overview.md) or [Edge API](../../dev-guide/api/edge/overview.md), or in the shell with the [Aembit CLI](../../dev-guide/cli/overview.md). Their home in the documentation is the [Developer Guide](../../dev-guide/overview.md).

The persona split is independent of the plane split, because every integration needs the management-plane configuration to exist first. Whether a security engineer creates that configuration in the Tenant UI or a pipeline creates it through Terraform changes nothing about how the workload retrieves its credential. Any pairing works: Agent Proxy with UI-managed configuration is the zero-code pattern, and the Edge SDK with Terraform is the everything-as-code pattern.

## How the planes interact at runtime

A single access request touches both planes in a fixed order:

1. An administrator or a pipeline defines the Access Policy in the management plane.
2. The management plane distributes the configuration to the control plane.
3. The workload makes a request, and an Aembit Edge component or a runtime path presents the workload’s attestation to the control plane.
4. The control plane verifies the identity, evaluates the Access Policy, and brokers the credential.
5. The credential returns to the Aembit Edge component or the workload, and the authorized request proceeds to the Server Workload.

![The management plane configures Access Policies while the control plane authorizes workload access at runtime](https://docs.aembit.io/d2/docs/get-started/concepts/planes-and-responsibilities-0.svg)

## Next steps

* **[Developer Guide overview](../../dev-guide/overview.md)** - Choose the runtime path for your workload and the interface for your configuration
* **[User Guide](../../user-guide/overview.md)** - Configure and administer the Access Policies and their components in the Tenant UI
* **[About Aembit Cloud](aembit-cloud.md)** - The internal architecture of the control plane and management plane
* **[About Aembit Edge](aembit-edge.md)** - The customer-side components that enforce authorization decisions
