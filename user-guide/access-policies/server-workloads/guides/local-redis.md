---
type: how-to
title: "Local Redis"
description: "This page describes how to configure Aembit to work with the local Redis Server Workload."
resource: https://docs.aembit.io/user-guide/access-policies/server-workloads/guides/local-redis/
interface: web-ui
tags: [databases, guide, server-workload, access-policy]
timestamp: 2026-03-04T13:50:35-08:00
type_inferred: true
---

# Local Redis

[Redis](https://redis.io/), an advanced key-value store, offers a fast and efficient solution for managing data in-memory. Use Redis for applications that require rapid access to cached information, real-time analytics, and message brokering. Redis supports a variety of data structures, including strings, hashes, lists, sets, and more. You can model and manipulate data based on your specific requirements.

Configure Aembit to work with Redis as a Server Workload using the Redis-compatible CLI, application, or a library. Note that Aembit doesn’t support the Redis inline command format.

## Prerequisites

[Section titled “Prerequisites”](#prerequisites)

* Access to a Kubernetes cluster

## Edit and deploy the Redis YAML file

[Section titled “Edit and deploy the Redis YAML file”](#edit-and-deploy-the-redis-yaml-file)

Edit this example file according to your specific configurations and then deploy it to your Kubernetes cluster.

Security best practice

This is an example file and not meant for production. Always use TLS in production environments.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: redis
    spec:
      containers:
      - name: redis
        image: redis
        imagePullPolicy: Always
        ports:
        - containerPort: 6379
          name: redis
        env:
        - name: MASTER
          value: "true"
        - name: REDIS_USER
          value: "<master user name>"
        - name: REDIS_PASSWORD
          value: "<master password>"
---
# Service
apiVersion: v1
kind: Service
metadata:
  name: redis
spec:
  type: NodePort
  selector:
    app: redis
  ports:
  - port: 6379
    targetPort: 6379
```

Deploy this file to your Kubernetes cluster:

`kubectl apply -f ./redis.yaml`

## Configure the Server Workload

[Section titled “Configure the Server Workload”](#configure-the-server-workload)

1. Create a new Server Workload:

   * **Name** - Choose a user-friendly name.

2. Configure the service endpoint:

   * **Host** - `redis.default.svc.cluster.local`
   * **Application Protocol** - Redis
   * **Port** - 6379
   * **Forward to Port** - 6379
   * **Authentication method** - Password Authentication
   * **Authentication scheme** - Password

## Configure the Credential Provider

[Section titled “Configure the Credential Provider”](#configure-the-credential-provider)

1. Create a new Credential Provider:

   * **Name** - Choose a user-friendly name.
   * **Credential Type** - [Username & Password](../../credential-providers/username-password.md)
   * **Username** - Provide the login ID for the Redis master user.
   * **Password** - Provide the master password associated with the Redis credentials.

## Configure the Client Workload

[Section titled “Configure the Client Workload”](#configure-the-client-workload)

Aembit handles the credentials required to access the Server Workload. You can remove any previously used credentials from the Client Workload.

If you access the Server Workload through an SDK or library, it may still require credentials for initialization. Provide placeholder credentials, as Aembit overwrites them during the access process.

## Create an Access Policy

[Section titled “Create an Access Policy”](#create-an-access-policy)

Create an Access Policy for a Client Workload to access the Redis Server Workload. Assign the newly created Credential Provider to it.

## Related

**Compatible credential providers**

* [Username & Password](../../credential-providers/username-password.md)
