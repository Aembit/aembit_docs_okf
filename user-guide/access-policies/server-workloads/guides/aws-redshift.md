---
type: how-to
title: "Amazon Redshift"
description: "This page describes how to configure Aembit to work with the Amazon Redshift Server Workload."
resource: https://docs.aembit.io/user-guide/access-policies/server-workloads/guides/aws-redshift/
interface: web-ui
tags: ["data-analytics", "guide", "server-workload", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Amazon Redshift


[Amazon Redshift](https://aws.amazon.com/redshift/) is a high-performance, fully managed cloud data warehouse designed for rapid query execution and storage of petabyte-scale datasets. This high-performance solution combines speed and scalability, making it ideal for businesses seeking efficient and flexible analytics capabilities in the cloud.

Below you can find the Aembit configuration required to work with Amazon Redshift as a Server Workload using the AWS or SQL-compatible CLI, application, or a library.

## Prerequisites

Before proceeding with the configuration, ensure you have an AWS tenant (or [sign up](https://portal.aws.amazon.com/billing/signup#/start/email) for one) and an Amazon Redshift managed cluster. If you haven’t created a cluster before, you can follow the steps in the next section. For more information on creating Amazon Redshift resources, please refer to the [official Amazon documentation](https://docs.aws.amazon.com/redshift/latest/mgmt/overview.html).

### Create a cluster with Amazon Redshift

1. Sign in to the AWS Management Console and navigate to the [Amazon Redshift console](https://console.aws.amazon.com/redshiftv2) and choose **Clusters** in the navigation pane.

![Amazon Redshift Clusters](https://docs.aembit.io/_astro/aws_redshift_clusters.DbRLECbT_Z2eRGfW.webp)

2. Click on **Create Cluster** and configure the cluster according to your preferences. Below are key choices:

* Under **Cluster configuration**, enter a name for the **cluster identifier**, which the endpoint uses.
* In **Database configurations**, set an **Admin user name**, and either auto-generate or provide an **Admin password**. Save this information for future use.

> **Note**
>
> In this example, this guide uses the `admin` username and password for demonstration purposes; however, it’s advisable to create a dedicated user with appropriate privileges for enhanced security.

* In **Additional configuration**, you may turn off **Use defaults** and customize settings further.
* In **Network and security**, find the **Publicly Accessible** option and check the box for **Turn on Publicly accessible**.

:warning: This guide sets the **Publicly Accessible** option to **Yes** purely for demonstration purposes. In normal circumstances, keep the instances not publicly accessible for enhanced security.

* In **Network and security**, ensure the **VPC security group (firewall)** configuration is in place to allow Client Workload/Agent Proxy communication.
* In **Database configurations**, specify a **Database name** and verify the **Database Port** is 5439.

3. After making all your selections, click **Create cluster**.

## Server Workload configuration

To retrieve the connection information for a cluster in the Amazon Redshift Console:

1. Sign in to the AWS Management Console and navigate to the [Amazon Redshift console](https://console.aws.amazon.com/redshiftv2).

2. In the left sidebar, select **Clusters** to view your clusters.

3. Click on the name of the cluster to view details.

4. In **General Information** copy the endpoint (excluding port and database name).

![Amazon Redshift Cluster Endpoint](https://docs.aembit.io/_astro/aws_redshift_cluster_endpoint.BibDjv1B_HKn5K.webp)

5. Create a new Server Workload.

* **Name** - Choose a user-friendly name.

6. Configure the service endpoint:

* **Host** - `<cluster-name>.<account-id>.<region>.redshift.amazonaws.com` (Provide the endpoint copied from AWS)
* **Application Protocol** - Amazon Redshift
* **Port** - 5439
* **Forward to Port** - 5439
* **Authentication method** - Password Authentication
* **Authentication scheme** - Password

## Credential Provider configuration

1. Create a new Credential Provider.

* **Name** - Choose a user-friendly name.
* **Credential Type** - [Username & Password](../../credential-providers/username-password.md)
* **Username** - Provide login ID for the administrator user of your cluster.
* **Password** - Provide the administrator password of your cluster.

## Client Workload configuration

Aembit now handles the credentials required to access the Server Workload, eliminating the need for you to manage them directly. You can remove any previously used credentials from the Client Workload.

If you access the Server Workload through an SDK or library, it’s possible that the SDK/library may still require credentials to be present for initialization purposes. In this scenario, you can provide placeholder credentials. Aembit overwrites these placeholder credentials with the appropriate ones during the access process.

## Access Policy

* Create an access policy for a Client Workload to access the Amazon Redshift Server Workload and assign the newly created Credential Provider to it.

## Related

**Compatible credential providers**

* [AWS STS Federation](../../credential-providers/aws-security-token-service-federation.md)
* [Username & Password](../../credential-providers/username-password.md)
