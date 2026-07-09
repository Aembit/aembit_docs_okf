---
type: how-to
title: "Amazon RDS for PostgreSQL"
description: "This page describes how to configure Aembit to work with the Amazon RDS for PostgreSQL Server Workload."
resource: https://docs.aembit.io/user-guide/access-policies/server-workloads/guides/aws-postgres/
interface: web-ui
tags: [databases, guide, server-workload, access-policy]
timestamp: 2026-07-07T18:22:50-07:00
type_inferred: true
---

# Amazon RDS for PostgreSQL


[Amazon RDS for PostgreSQL](https://aws.amazon.com/rds/postgresql) is a fully managed relational database service provided by Amazon Web Services. It offers a scalable and efficient solution for deploying, managing, and scaling PostgreSQL databases in the cloud.

Below you can find the Aembit configuration required to work with AWS RDS for PostgreSQL as a Server Workload using PostgreSQL-compatible CLI, application, or a library.

## Prerequisites

[Section titled “Prerequisites”](#prerequisites)

Before proceeding with the configuration, ensure you have an AWS tenant (or [sign up](https://portal.aws.amazon.com/billing/signup#/start/email) for one) and an Amazon RDS for PostgreSQL database. If you haven’t created a database before, you can follow the steps in the next section. For more information on creating an Amazon RDS DB instance, please refer to the [official Amazon documentation](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Tutorials.WebServerDB.CreateDBInstance.html).

### Create Amazon RDS PostgreSQL database

[Section titled “Create Amazon RDS PostgreSQL database”](#create-amazon-rds-postgresql-database)

1. Sign in to the AWS Management Console and navigate to the [Amazon RDS console](https://console.aws.amazon.com/rds/).

2. In the left sidebar, select **Databases**, and then click **Create Database** in the top right corner.

![AWS RDS Create Database](https://docs.aembit.io/_astro/aws_rds_create_database.Bd9Yx41r_ZUfsQV.webp)

3. Configure the database according to your preferences. Below are key choices:

* Under **Engine options**, choose **PostgreSQL** for the engine type.

* Under **Engine options**, select a version **16** or from the **15** series.

* Under **Settings**, enter a name for the **DB cluster identifier**, which the endpoint uses.

* In **Settings**, expand the **Credentials Settings** section. Use the **Master username** and **master password** as Credential Provider details. You can either auto-generate a password or type your own. Save this information for future use.

  Note

  In this example, this guide uses the master username and password for demonstration purposes; however, it’s advisable to create a dedicated user with appropriate privileges for enhanced security.

* In **Connectivity**, find the **Publicly Accessible** option and set it to **Yes**.

:warning: This guide sets the **Publicly Accessible** option to **Yes** purely for demonstration purposes. In normal circumstances, keep the RDS instance not publicly accessible for enhanced security.

* In **Connectivity**, ensure the **VPC security group (firewall)** configuration is in place to allow client workload/agent proxy communication.
* In **Connectivity**, expand the **Additional Configuration** section and verify the **Database Port** is 5432.
* In **Database authentication**, select **Password authentication**.
* In **Additional configuration**, specify an **Initial database name**.

4. After making all your selections, click **Create Database**.

## Server Workload configuration

[Section titled “Server Workload configuration”](#server-workload-configuration)

To retrieve the connection information for a DB instance in the AWS Management Console:

1. Sign in to the AWS Management Console and navigate to the [Amazon RDS console](https://console.aws.amazon.com/rds/).

2. In the left sidebar, select **Databases** to view a list of your DB instances.

3. Click on the name of the DB instance to view its details.

4. Navigate to the **Connectivity & security** tab and copy the endpoint.

![AWS RDS Database Endpoint](https://docs.aembit.io/_astro/aws_postgres_endpoint.CPvI6mLN_2uzCSi.webp)

5. Create a new Server Workload.

* **Name** - Choose a user-friendly name.

6. Configure the service endpoint:

* **Host** - `<db-instance-name>.<account-id>.<region>.rds.amazonaws.com` (Provide the endpoint copied from AWS)
* **Application Protocol** - PostgreSQL
* **Port** - 5432
* **Forward to Port** - 5432 with TLS
* **Forward TLS Verification** - Full
* **Authentication method** - Password Authentication
* **Authentication scheme** - Password

## Credential Provider configuration

[Section titled “Credential Provider configuration”](#credential-provider-configuration)

1. Create a new Credential Provider.

* **Name** - Choose a user-friendly name.
* **Credential Type** - [Username & Password](../../credential-providers/username-password.md)
* **Username** - Provide login ID for the master user of your DB cluster.
* **Password** - Provide the Master password of your DB cluster.

## Client Workload configuration

[Section titled “Client Workload configuration”](#client-workload-configuration)

Aembit now handles the credentials required to access the Server Workload, eliminating the need for you to manage them directly. You can remove any previously used credentials from the Client Workload.

If you access the Server Workload through an SDK or library, it’s possible that the SDK/library may still require credentials to be present for initialization purposes. In this scenario, you can provide placeholder credentials. Aembit overwrites these placeholder credentials with the appropriate ones during the access process.

## Access Policy

[Section titled “Access Policy”](#access-policy)

* Create an access policy for a Client Workload to access the Amazon RDS for PostgreSQL Server Workload and assign the newly created Credential Provider to it.

## Related

**Compatible credential providers**

* [AWS STS Federation](../../credential-providers/aws-security-token-service-federation.md)
* [Username & Password](../../credential-providers/username-password.md)
