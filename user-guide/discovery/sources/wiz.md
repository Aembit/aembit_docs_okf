---
type: how-to
title: "Wiz Discovery Source"
description: "How Aembit discovers workloads using the Wiz Discovery Source"
resource: https://docs.aembit.io/user-guide/discovery/sources/wiz/
interface: web-ui
tags: ["source", "discovery"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Wiz Discovery Source

> **Note**
>
> This is a beta feature and may be subject to changes. To enable Discovery with Wiz, contact Aembit by completing the [Contact Us form](https://aembit.io/contact/).

This page explains how Aembit uses the Wiz Discovery Source to identify workloads in your cloud environments.

The [Wiz Discovery Integration](../../administration/discovery/integrations/wiz.md) allows Aembit to pull workload data from your Wiz tenant through the Wiz Integration API.

Once integrated, Aembit automatically fetches workload data from your Wiz tenant and imports it as discovered workloads—draft entities you can review and optionally manage within Aembit.

**Wiz Discovery** simplifies the process of discovering workloads in cloud environments by seamlessly syncing data from Wiz into Aembit. This integration provides Aembit with a comprehensive, up-to-date view of your workloads, enabling you to apply Access Policies and make informed decisions about managing your cloud resources.

### How to perform wiz discovery

1. **Configure the Wiz Integration** - Follow the [Wiz Discovery Integration](../../administration/discovery/integrations/wiz.md) guide to configure the integration. This step make sure that Aembit can securely connect to your Wiz environment and begin syncing data.

2. **Sync the Data** - After saving the integration, Aembit starts syncing data from Wiz. The initial sync may take longer than subsequent syncs, as it pulls in all relevant workload data from Wiz.

3. **Review Discovered Workloads** - After syncing, Aembit displays the discovered workloads in the **Discovered** tab. These workloads aren’t yet managed by Aembit, so you can review them and categorize them according to your security and Access Policies.

   > **Note**
   >
   > After the initial sync, Aembit compares future syncs to the previously retrieved data. If you add new workloads in Wiz, Aembit won’t detect them until they become available in the Wiz environment.

By following these steps, Aembit fetches and syncs the latest workload data from your Wiz environment. This streamlines the process of managing workloads in the cloud.

After syncing, Aembit categorizes the workloads as discovered and displays them for further review. You can then choose to manage them, apply Access Policies, or take other appropriate actions. To interact with or manage the discovered workloads, visit [Interacting with Discovered Workloads](../managing-discovered-workloads.md) for more details.

## Wiz-discoverable resource types

The following lists represent all the available resource types that Aembit can discover through Wiz:

### Client Workload resources

* AWS ECS Task
* AWS EC2 Virtual Machine
* Azure Virtual Machine
* GCP Virtual Machine
* Kubernetes Deployments

### Server Workload resources

* AWS Redshift

* AWS RDS

  * Aurora Postgres Clusters
  * Postgres Clusters
  * MySql Clusters
  * Postgres Instances
  * MariaDB Instances
  * MySql Instances
  * Aurora MySQL Instances
  * Aurora Postgres Instances

* AWS Elasticache

  * Redis Clusters
  * Valkey Clusters
  * Redis Serverless Instance
  * Valkey Serverless Instance

* AWS EC2 Load Balancers

  * V1
  * V2 Application
  * V2 Network

* AWS Lambda

* AWS S3 Buckets

* Azure Database

  * Postgres
  * MySql

* Azure Load Balancers

* Azure Blob Storage

* GCP BigQuery

* GCP Database

  * Postgres
  * MySql
