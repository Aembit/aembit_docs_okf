---
type: explanation
title: "Aembit User Guide Overview"
description: "How to set up and use Aembit"
resource: https://docs.aembit.io/user-guide/
timestamp: 2026-06-18T13:15:52-04:00
type_inferred: true
---

# Aembit User Guide Overview

Welcome to the Aembit User Guide!

Use this guide to help you understand, deploy, and manage Aembit’s Workload Identity and Access Management Platform. This guide contains the following main sections, each focusing on different aspects of Aembit’s functionality and configuration.

## Deploy and install

[Section titled “Deploy and install”](#deploy-and-install)

This section covers how to deploy Aembit Edge Components in different environments and configurations. It provides detailed instructions for setting up Aembit in different infrastructure contexts.

This section includes topics covering:

* [Kubernetes Deployment](deploy-install/kubernetes/kubernetes.md)
* [Virtual Machine Deployment](deploy-install/virtual-machine/overview.md)
* [Serverless Deployment](deploy-install/serverless/overview.md)
* [Virtual Appliance Deployment](deploy-install/virtual-appliances/overview.md)

## Access Policies

[Section titled “Access Policies”](#access-policies)

This section details how to configure and manage access policies, which are the core components that define and enforce workload access controls. You’ll learn how to create and manage the different elements that make up effective access policies.

This section includes topics covering:

* [Client Workloads](access-policies/client-workloads/overview.md)
* [Server Workloads](access-policies/server-workloads/guides/overview.md)
* [Trust Providers](access-policies/trust-providers/overview.md)
* [Credential Providers](access-policies/credential-providers/overview.md)
* [Access Conditions](access-policies/access-conditions/overview.md)

## Administration

[Section titled “Administration”](#administration)

This section focuses on managing your Aembit Tenant and its administration features. It covers tasks related to user management, roles, and other administrative functions to help you maintain your Aembit environment.

This section includes topics covering:

* [Admin Dashboard](administration/admin-dashboard/overview.md)
* [Users Management](administration/users/overview.md)
* [Roles](administration/roles/overview.md)
* [Resource Sets](administration/resource-sets/overview.md)
* [Sign-On Policy](administration/sign-on-policy/overview.md)
* [Identity Providers](administration/identity-providers/overview.md)
* [Log Streams](administration/log-streams/overview.md)

## Audit and report

[Section titled “Audit and report”](#audit-and-report)

This section covers the reporting and auditing capabilities of Aembit. It helps you understand how to monitor access events and activities within your Aembit environment for security and compliance purposes.

This section includes topics covering:

* [Access Authorization Events](audit-report/access-authorization-events.md)
* [Audit Logs](audit-report/audit-logs.md)

## Reference

[Section titled “Reference”](#reference)

This section provides technical reference materials such as environment variables, configuration options, and compatibility information. It serves as a quick reference guide for specific technical details about Aembit components.

This section includes topics covering:

* [Edge Component Supported Versions](../reference/edge-components/edge-component-supported-versions.md)
* [Edge Component Log Level Reference](../reference/edge-components/agent-log-level-reference.md)
* [Edge Component Environment Variables Reference](../reference/edge-components/edge-component-env-vars.md)
* [Edge Component Helm Chart Configuration Options Reference](../reference/edge-components/helm-chart-config-options.md)
* [Support Matrix](../reference/support-matrix.md)

## Troubleshooting and support

[Section titled “Troubleshooting and support”](#troubleshooting-and-support)

The Troubleshooting and Support section provides practical guidance for resolving common issues and accessing help when needed because even well-designed systems occasionally encounter problems that require diagnosis and resolution. This section serves as your resource for maintaining operational continuity with Aembit.

This section includes topics covering:

* [Troubleshooting](troubleshooting/overview.md)
* [Agent Controller Health](troubleshooting/agent-controller-health.md)
* [Agent Proxy Debug Network Tracing](troubleshooting/agent-proxy-debug-network-tracing.md)
* [Tenant Health Check](troubleshooting/tenant-health-check.md)
