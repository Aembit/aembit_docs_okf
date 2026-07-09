---
type: how-to
title: "AWS EKS Fargate"
description: "Aembit Edge Component deployment considerations in an EKS Fargate environment"
resource: https://docs.aembit.io/user-guide/deploy-install/kubernetes/aws-eks-fargate/
interface: web-ui
tags: [kubernetes, deploy-install]
timestamp: 2025-08-22T15:17:43-07:00
type_inferred: true
---

# AWS EKS Fargate

Aembit provides different deployment options that you can use to deploy Aembit Edge Components in your environment. Each of these options provides similar features and functionality. The steps for each of these options, however, are specific to the deployment option you select.

This page describes the extra considerations that apply to AWS EKS Fargate that differ from the standard Kubernetes deployment.

AWS Elastic Kubernetes Service (EKS) Fargate is a serverless Kubernetes solution, where EKS automatically provisions and scales the compute capacity for pods. To schedule pods on Fargate in your EKS cluster, instead of on EC2 instances that you manage, you must define a [Fargate profile](https://docs.aws.amazon.com/eks/latest/userguide/fargate-profile.html). Fargate profiles provide a selector based on `namespace` and (optionally) `labels`, pods that match the selector will be scheduled on Fargate.

## Deployment considerations

[Section titled “Deployment considerations”](#deployment-considerations)

In general, the same deployment steps should be undertaken as described in the [Kubernetes](kubernetes.md) page. However, you must use a namespace that matches the Fargate profile selector so that Aembit schedules Edge Components on Fargate with the Client Workload. You must provide this namespace when deploying the Aembit Edge Helm chart.

For example:

```shell
helm install aembit aembit/aembit \
  -n <namespace-that-matches-fargate-profile-selector> \
  --create-namespace \
  --set ...
```

## Limitations

[Section titled “Limitations”](#limitations)

You must use the [Explicit Steering](../advanced-options/agent-proxy/explicit-steering.md#configure-explicit-steering) feature when deploying in AWS EKS Fargate. This is a limitation of the AWS Fargate serverless environment, which intentionally restricts network configuration, preventing advanced networking features like transparent steering.
