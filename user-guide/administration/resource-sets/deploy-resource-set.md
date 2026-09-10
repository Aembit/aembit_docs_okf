---
type: how-to
title: "How to deploy a Resource Set"
description: "How to deploy a Resource Set"
resource: https://docs.aembit.io/user-guide/administration/resource-sets/deploy-resource-set/
interface: web-ui
tags: ["resource-set", "administration"]
timestamp: 2026-09-08T23:32:41-07:00
---

# How to deploy a Resource Set

Once a Resource Set has been created, and roles and responsibilities have been assigned, the Agent Proxy component needs to be configured and deployed to work with the specific [`AEMBIT_RESOURCE_SET_ID` Agent Proxy environment variable](../../../reference/edge-components/edge-component-env-vars.md#agent-proxy-environment-variables). All Aembit deployment mechanisms are supported, including:

* Kubernetes
* Terraform ECS Module
* Agent Proxy VM Installer
* AWS Lambda

### Kubernetes deployment

To deploy a Resource Set using Kubernetes, you need to add the `aembit.io/resource-set-id` annotation to your Client Workload deployments.

For more information on how to deploy Resource Sets using Kubernetes, please see the [Kubernetes Deployment](../../deploy-install/kubernetes/kubernetes.md) page.

### Terraform ECS module deployment

To deploy a Resource Set using the Terraform ECS Module, you need to provide the `AEMBIT_RESOURCE_SET_ID` environment variable in the Client Workload ECS Task.

For more detailed information on how to deploy a Resource Set using the Terraform ECS Module, please see the [Terraform Configuration](../../access-policies/advanced-options/terraform/terraform-configuration.md#resources-and-data-sources) page.

### Agent Proxy VM installer deployment

To deploy a Resource Set using the Agent Proxy Virtual Machine Installer, you need to specify the `AEMBIT_RESOURCE_SET_ID` environment variable during the Agent Proxy installation.

For more information on how to deploy a Resource Set using the Agent Proxy Virtual Machine Installer, please see the [Virtual Machine Installation](../../deploy-install/virtual-machine/overview.md) page.

### AWS Lambda deployment

To deploy a Resource Set using an AWS Lambda Container, you need to specify the `AEMBIT_RESOURCE_SET_ID` environment variable to your Client Workload.

For more information on AWS Lambda deployment environments, see the [AWS Lambda function](../../deploy-install/serverless/aws-lambda-function.md) and [AWS Lambda container](../../deploy-install/serverless/aws-lambda-container.md) pages.
