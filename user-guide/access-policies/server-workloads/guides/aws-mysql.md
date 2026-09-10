---
type: how-to
title: "Amazon RDS for MySQL"
description: "This page describes how to configure Aembit to work with the Amazon RDS for MySQL Server Workload."
resource: https://docs.aembit.io/user-guide/access-policies/server-workloads/guides/aws-mysql/
interface: web-ui
tags: ["databases", "guide", "server-workload", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Amazon RDS for MySQL


[Amazon RDS for MySQL](https://aws.amazon.com/rds/mysql/) is a robust and fully managed relational database service provided by Amazon Web Services, specifically tailored to streamline the deployment, administration, and scalability of MySQL databases in the cloud.

Below you can find the Aembit configuration required to work with AWS RDS for MySQL as a Server Workload using MySQL-compatible CLI, application, or a library.

## Prerequisites

Before proceeding with the configuration, ensure you have an AWS tenant (or [sign up](https://portal.aws.amazon.com/billing/signup#/start/email) for one) and an Amazon RDS for MySQL database. If you have not created a database before, you can follow the steps in the next section. For more information on creating an Amazon RDS DB instance, please refer to the [official Amazon documentation](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Tutorials.WebServerDB.CreateDBInstance.html).

### Create Amazon RDS MySQL Database

1. Sign in to the AWS Management Console and navigate to the [Amazon RDS console](https://console.aws.amazon.com/rds/).

2. In the left sidebar, select **Databases**, and then click **Create Database** in the top right corner.

![AWS RDS Create Database](https://docs.aembit.io/_astro/aws_rds_create_database.Bd9Yx41r_ZUfsQV.webp)

3. Configure the database according to your preferences. Below are key choices:

* Under **Engine options**, choose **MySQL** for the engine type.

* Under **Engine options**, select a version from the **8.0.x** series.

* Under **Settings**, enter a name for the **DB cluster identifier**; this will be used in the endpoint.

* In **Settings**, expand the **Credentials Settings** section. Use the **Master username** and **master password** as Credential Provider details. You can either auto-generate a password or type your own. Save this information for future use.

  > **Note**
  >
  > In this example, we are using the master username and password for demonstration purposes; however, it is advisable to create a dedicated user with appropriate privileges for enhanced security.

* In **Connectivity**, find the **Publicly Accessible** option and set it to **Yes**.

:warning: Setting the **Publicly Accessible** option to **Yes** is done here purely for demonstration purposes. In normal circumstances, it is recommended to keep the RDS instance not publicly accessible for enhanced security.

* In **Connectivity**, ensure the **VPC security group (firewall)** configuration is in place to allow client workload/agent proxy communication.
* In **Connectivity**, expand the **Additional Configuration** section and verify the **Database Port** is set to 3306.
* In **Database authentication**, select **Password authentication**.
* In **Additional configuration**, specify an **Initial database name**.

4. After making all of your selections, click **Create Database**.

## Server Workload Configuration

To retrieve the connection information for a DB instance in the AWS Management Console:

1. Sign in to the AWS Management Console and navigate to the [Amazon RDS console](https://console.aws.amazon.com/rds/).

2. In the left sidebar, select **Databases** to view a list of your DB instances.

3. Click on the name of the DB instance to view its details.

4. Navigate to the **Connectivity & security** tab and copy the endpoint.

![AWS RDS Database Endpoint](https://docs.aembit.io/_astro/aws_mysql_endpoint.Cx_LjOqb_ZRFHOs.webp)

5. Create a new Server Workload.

* **Name** - Choose a user-friendly name.

6. Configure the service endpoint:

* **Host** - `<db-instance-name>.<account-id>.<region>.rds.amazonaws.com` (Provide the endpoint copied from AWS)
* **Application Protocol** - MySQL
* **Port** - 3306
* **Forward to Port** - 3306 with TLS
* **Forward TLS Verification** - Full
* **Authentication method** - Password Authentication
* **Authentication scheme** - Password

## Credential Provider Configuration

1. Create a new Credential Provider.

* **Name** - Choose a user-friendly name.
* **Credential Type** - [Username & Password](../../credential-providers/username-password.md)
* **Username** - Provide login ID for the master user of your DB cluster.
* **Password** - Provide the Master password of your DB cluster.

## Client Workload Configuration

Aembit now handles the credentials required to access the Server Workload, eliminating the need for you to manage them directly. You can safely remove any previously used credentials from the Client Workload.

If you access the Server Workload through an SDK or library, it is possible that the SDK/library may still require credentials to be present for initialization purposes. In this scenario, you can provide placeholder credentials. Aembit will overwrite these placeholder credentials with the appropriate ones during the access process.

## Access Policy

* Create an access policy for a Client Workload to access the Amazon RDS for MySQL Server Workload and assign the newly created Credential Provider to it.

## Related

**Compatible credential providers**

* [AWS STS Federation](../../credential-providers/aws-security-token-service-federation.md)
* [Username & Password](../../credential-providers/username-password.md)
