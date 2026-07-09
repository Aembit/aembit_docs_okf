---
type: how-to
title: "Local PostgreSQL"
description: "This page describes how to configure Aembit to work with the local PostgreSQL Server Workload."
resource: https://docs.aembit.io/user-guide/access-policies/server-workloads/guides/local-postgres/
interface: web-ui
tags: [databases, guide, server-workload, access-policy]
timestamp: 2026-07-07T18:22:50-07:00
type_inferred: true
---

# Local PostgreSQL


[PostgreSQL](https://www.postgresql.org/) stands out as a dynamic and versatile relational database service, delivering scalability and efficiency. This solution facilitates the effortless deployment, administration, and scaling of PostgreSQL databases in diverse cloud settings.

Below you can find the Aembit configuration required to work with PostgreSQL as a Server Workload using PostgreSQL-compatible CLI, application, or a library.

## Prerequisites

[Section titled “Prerequisites”](#prerequisites)

Before proceeding with the configuration, ensure you have access to a Kubernetes cluster. Modify the example YAML file according to your specific configurations, and then deploy it to your Kubernetes cluster.

### Example PostgreSQL YAML file

[Section titled “Example PostgreSQL YAML file”](#example-postgresql-yaml-file)

Note

This example doesn’t use TLS and appears here for demonstration purposes only. It’s strongly recommended to use TLS in production settings.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: postgresql
spec:
  selector:
    matchLabels:
      app: postgresql
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: postgresql
    spec:
      containers:
      - image: postgres:16.0
        name: postgresql
        env:
        - name: POSTGRES_DB
          value: <database name>
        - name: POSTGRES_USER
          value: <master user name>
        - name: POSTGRES_PASSWORD
          value: "<master password>"
        ports:
        - containerPort: 5432
          name: postgresql
---
# Service
apiVersion: v1
kind: Service
metadata:
  name: postgresql
  annotations:
spec:
  type: NodePort
  ports:
  - name: postgresql
    port:  5432
    targetPort: 5432
  selector:
    app: postgresql
```

:warning: Before running the command, ensure you have replaced the master user name, master password and database name in the configuration file with your desired values.

Use the following command to deploy this file to your Kubernetes cluster.

`kubectl apply -f ./postgres.yaml`

## Server Workload configuration

[Section titled “Server Workload configuration”](#server-workload-configuration)

1. Create a new Server Workload.

* **Name** - Choose a user-friendly name.

2. Configure the service endpoint:

* **Host** - `postgres.default.svc.cluster.local`
* **Application Protocol** - PostgreSQL
* **Port** - 5432
* **Forward to Port** - 5432
* **Authentication method** - Password Authentication
* **Authentication scheme** - Password

## Credential Provider configuration

[Section titled “Credential Provider configuration”](#credential-provider-configuration)

1. Create a new Credential Provider.

* **Name** - Choose a user-friendly name.
* **Credential Type** - [Username & Password](../../credential-providers/username-password.md)
* **Username** - Provide the database login ID for the PostgreSQL master user.
* **Password** - Provide the master password associated with the PostgreSQL database credentials.

## Client Workload configuration

[Section titled “Client Workload configuration”](#client-workload-configuration)

Aembit now handles the credentials required to access the Server Workload, eliminating the need for you to manage them directly. You can remove any previously used credentials from the Client Workload.

If you access the Server Workload through an SDK or library, it’s possible that the SDK/library may still require credentials to be present for initialization purposes. In this scenario, you can provide placeholder credentials. Aembit overwrites these placeholder credentials with the appropriate ones during the access process.

## Access Policy

[Section titled “Access Policy”](#access-policy)

* Create an access policy for a Client Workload to access the PostgreSQL Server Workload and assign the newly created Credential Provider to it.

## Related

**Compatible credential providers**

* [Username & Password](../../credential-providers/username-password.md)
