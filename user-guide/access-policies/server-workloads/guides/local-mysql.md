---
type: how-to
title: "Local MySQL"
description: "This page describes how to configure Aembit to work with the local MySQL Server Workload."
resource: https://docs.aembit.io/user-guide/access-policies/server-workloads/guides/local-mysql/
interface: web-ui
tags: [databases, guide, server-workload, access-policy]
timestamp: 2025-05-29T11:26:12-07:00
type_inferred: true
---

# Local MySQL


[MySQL](https://www.mysql.com/) is a powerful and widely-used open-source relational database management system, commonly used for local development environments and applications of various scales, while providing a intense foundation for efficient data storage, retrieval, and management.

Below you can find the Aembit configuration required to work with MySQL as a Server Workload using the MySQL-compatible CLI, application, or a library.

## Prerequisites

[Section titled “Prerequisites”](#prerequisites)

Before proceeding with the configuration, ensure you have access to a Kubernetes cluster. Modify the example YAML file according to your specific configurations, and then deploy it to your Kubernetes cluster.

### Example MySQL Yaml File

[Section titled “Example MySQL Yaml File”](#example-mysql-yaml-file)

Note

This example does not use TLS and is shown here for demonstration purposes only. It is strongly recommended to use TLS in production settings.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql
spec:
  selector:
    matchLabels:
      app: mysql
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - image: mysql:5.7.44
        name: mysql
        args: ["--ssl=0"]
        env:
        - name: MYSQL_ROOT_PASSWORD
          value: "<master password>"
        - name: MYSQL_DATABASE
          value: <database name>
        ports:
        - containerPort: 3306
          name: mysql
---
# Service
apiVersion: v1
kind: Service
metadata:
  name: mysql
  annotations:
spec:
  type: NodePort
  ports:
  - name: mysql
    port:  3306
    targetPort: 3306
  selector:
    app: mysql
```

:warning: Before running the command, ensure you have replaced the master password and database name in the configuration file with your desired values.

Use the following command to deploy this file to your Kubernetes cluster.

`kubectl apply -f ./mysql.yaml`

## Server Workload Configuration

[Section titled “Server Workload Configuration”](#server-workload-configuration)

1. Create a new Server Workload.

* **Name** - Choose a user-friendly name.

2. Configure the service endpoint:

* **Host** - `mysql.default.svc.cluster.local`
* **Application Protocol** - MySQL
* **Port** - 3306
* **Forward to Port** - 3306
* **Authentication method** - Password Authentication
* **Authentication scheme** - Password

## Credential Provider Configuration

[Section titled “Credential Provider Configuration”](#credential-provider-configuration)

1. Create a new Credential Provider.

* **Name** - Choose a user-friendly name.
* **Credential Type** - [Username & Password](../../credential-providers/username-password.md)
* **Username** - Provide the database login ID for the MySQL master user.
* **Password** - Provide the master password associated with the MySQL database credentials.

## Client Workload Configuration

[Section titled “Client Workload Configuration”](#client-workload-configuration)

Aembit now handles the credentials required to access the Server Workload, eliminating the need for you to manage them directly. You can safely remove any previously used credentials from the Client Workload.

If you access the Server Workload through an SDK or library, it is possible that the SDK/library may still require credentials to be present for initialization purposes. In this scenario, you can provide placeholder credentials. Aembit will overwrite these placeholder credentials with the appropriate ones during the access process.

## Access Policy

[Section titled “Access Policy”](#access-policy)

* Create an access policy for a Client Workload to access the MySQL Server Workload and assign the newly created Credential Provider to it.

## Related

**Compatible credential providers**

* [Username & Password](../../credential-providers/username-password.md)
