---
type: how-to
title: "Snowflake"
description: "This page describes how to configure Aembit to work with the Snowflake Server Workload."
resource: https://docs.aembit.io/user-guide/access-policies/server-workloads/guides/snowflake/
interface: web-ui
tags: ["data-analytics", "guide", "server-workload", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Snowflake


[Snowflake](https://www.snowflake.com/) is a cloud-based data platform that revolutionizes the way organizations handle and analyze data. Snowflake’s architecture allows for seamless and scalable data storage and processing, making it a powerful solution for modern data analytics and warehousing needs.

In the sections below, you can find the required Aembit configuration needed to work with the Snowflake service as a Server Workload. This page describes scenarios where the Client Workload accesses Snowflake via:

* the [Snowflake Driver/Connector](snowflake.md#snowflake-via-driverconnector) embedded in Client Workload.
* the [Snowflake SQL Rest API](snowflake.md#snowflake-sql-rest-api).

## Prerequisites

Before proceeding with the configuration, you must have a Snowflake tenant (or [sign up](https://signup.snowflake.com/) for one).

## Snowflake via Driver/Connector

This section of the guide is tailored to scenarios where the Client Workload interacts with Snowflake through the [Snowflake Driver/Connector](https://docs.snowflake.com/en/developer-guide/drivers) embedded in the Client Workload.

### Snowflake key-pair authentication

Snowflake key-pair authentication, when applied to workloads, involves using a public-private key pair for secure, automated authentication. Aembit generates and securely stores a private key, while the corresponding public key is registered with Snowflake. This setup allows Aembit to authenticate with Snowflake, leveraging the robust security of asymmetric encryption, without relying on conventional user-based passwords. For more information on key-pair authentication and key-pair rotation, please refer to the [official Snowflake documentation](https://docs.snowflake.com/en/user-guide/key-pair-auth#configuring-key-pair-rotation).

#### Server Workload Configuration

1. Create a new Server Workload.

* **Name** - Choose a user-friendly name.

2. Configure the service endpoint:

* **Host** - `<organization-name>-<account-name>.snowflakecomputing.com`
* **Application Protocol** - Snowflake
* **Port** - 443 with TLS
* **Forward to Port** - 443 with TLS
* **Authentication method** - JWT Token Authentication
* **Authentication scheme** - Snowflake JWT

#### Credential provider configuration

1. Sign into your Snowflake account.

2. Click in the bottom left corner and copy the Locator value for use in the Aembit Snowflake Account ID field.

![Copy Locator Value](https://docs.aembit.io/_astro/snowflake_locator_value.BZFxOCPC_Z1Nf2df.webp)

3. Create a new Credential Provider.

* **Name** - Choose a user-friendly name.
* **Credential Type** - [JSON Web Token (JWT)](../../credential-providers/json-web-token.md)
* **Token Configuration** - Snowflake Key Pair Authentication
* **Snowflake Account ID** - Your Snowflake Locator value that you copied from the previous step.
* **Username** - Your username for the Snowflake account.

4. Click **Save**.

![Snowflake JWT Credentials on Aembit Edge UI](https://docs.aembit.io/_astro/snowflake_JWT_credentials.DlYU24ZC_Z29yk3r.webp)

5. After saving the Credential Provider, view the newly created provider and copy the provided SQL command. This command needs to be executed against your Snowflake account. You can use any tool of your choice that supports Snowflake to execute this command.

### Snowflake username/password authentication

> **Note**
>
> Aembit will be deprecating Snowflake username/password authentication to match Snowflake’s updated MFA security guidance.

Username/password authentication in Snowflake involves using a traditional credential-based approach for access control. Users or workloads are assigned a unique username and a corresponding password. When accessing Snowflake, the username and password are used to verify identity. Username/password authentication in Snowflake is considered less secure than key pair authentication and is typically used when key pair methods are not feasible.

#### Server Workload Configuration

1. Create a new Server Workload.

* Name: Choose a user-friendly name.

2. Configure the service endpoint:

* **Host** - `<organization-name>-<account-name>.snowflakecomputing.com`
* **Application Protocol** - Snowflake
* **Port** - 443 with TLS
* **Forward to Port** - 443 with TLS
* **Authentication method** - Password Authentication
* **Authentication scheme** - Password

#### Credential Provider Configuration

1. Create a new Credential Provider.

* **Name** - Choose a user-friendly name.
* **Credential Type** - [Username & Password](../../credential-providers/username-password.md)
* **Username** - Your username for the Snowflake account.
* **Password** - Your password for the account.

## Snowflake SQL REST API

This section focuses on scenarios where the Client Workload interacts with Snowflake through the [Snowflake SQL REST API](https://docs.snowflake.com/en/developer-guide/sql-api/). The Snowflake SQL REST API offers a flexible REST API for accessing and modifying data within a Snowflake database.

### Server Workload Configuration

1. Create a new Server Workload.

* **Name** - Choose a user-friendly name.

2. Configure the service endpoint:

* **Host** - `<organization-name>-<account-name>.snowflakecomputing.com`
* **Application Protocol** - HTTP
* **Port** - 443 with TLS
* **Forward to Port** - 443 with TLS
* **Authentication method** - HTTP Authentication
* **Authentication scheme** - Bearer

**Static HTTP Headers**

* **Key** - X-Snowflake-Authorization-Token-Type
* **Value** - KEYPAIR\_JWT

### Credential provider configuration

1. Sign into your Snowflake account.

2. Click in the bottom left corner and copy the Locator value for use in the Aembit Snowflake Account ID field.

![Copy Locator Value](https://docs.aembit.io/_astro/snowflake_locator_value.BZFxOCPC_Z1Nf2df.webp)

3. Create a new Credential Provider.

* **Name** - Choose a user-friendly name.
* **Credential Type** - [JSON Web Token (JWT)](../../credential-providers/json-web-token.md)
* **Token Configuration** - Snowflake Key Pair Authentication
* **Snowflake Account ID** - Your Snowflake Locator value that you copied from the previous step.
* **Username** - Your username for the Snowflake account.

4. Click **Save**.

![Snowflake JWT Credentials on Aembit Edge UI](https://docs.aembit.io/_astro/snowflake_JWT_credentials.DlYU24ZC_Z29yk3r.webp)

5. After saving the Credential Provider, view the newly created provider and copy the provided SQL command. This command needs to be executed against your Snowflake account. You can use any tool of your choice that supports Snowflake to execute this command.

## Client Workload Configuration

Aembit now handles the credentials required to access the Server Workload, eliminating the need for you to manage them directly. You can safely remove any previously used credentials from the Client Workload.

If you access the Server Workload through an SDK or library, it is possible that the SDK/library may still require credentials to be present for initialization purposes. In this scenario, you can provide placeholder credentials. Aembit will overwrite these placeholder credentials with the appropriate ones during the access process.

## Access Policy

* Create an Access Policy for a Client Workload to access the Snowflake Server Workload. Assign the newly created Credential Provider to this Access Policy.

## Required Features

* You will need to configure the [TLS Decrypt](../../../deploy-install/advanced-options/tls-decrypt/configure-tls-decrypt.md) feature to work with the Snowflake Server Workload.

> **Caution**
>
> As of Snowflake SDK 2.1.0, proxy settings must be explicitly specified within the connection string. In prior versions, the SDK automatically utilized proxy configurations based on environment variables such as `http_proxy` or `https_proxy`.
>
> For instance, if you are deploying the SDK within an ECS environment, it is essential to include the following parameters in your connection string:
>
> ```shell
> USEPROXY=true;PROXYHOST=localhost;PROXYPORT=8000
> ```

## Related

**Compatible credential providers**

* [JSON Web Token (JWT)](../../credential-providers/json-web-token.md)
