---
type: explanation
title: "Get started with Aembit Cloud API"
description: "Overview of how to get started learning about and using Aembit Cloud API"
resource: https://docs.aembit.io/api-guide/cloud/
tags: [cloud]
timestamp: 2025-06-17T16:37:36-07:00
type_inferred: true
---

# Get started with Aembit Cloud API

The Aembit API is a lightweight, RESTful API that enables clients to make calls to various API endpoints using standard HTTP URL syntax to perform Create, Read, Update and Delete (CRUD) tasks, and access different types of resources and information. Requests are made using URL and JSON request format, and responses are returned in easy-to-read JSON format.

Create, Read, Update and Delete operations are performed using the following REST verbs:

* **Create** - `POST`
* **Read** - `GET`
* **Update** - `PUT` or `PATCH`
* **Delete** - `DELETE`

Adhering to REST standards and best practices, the Aembit API was designed to be easy-to-use, with minimal complexity or sophistication.

Note

The Aembit API documentation assumes you already have a base level understanding of REST API concepts, terminology, and syntax. If you are not familiar with REST, or simply need a refresher, please see the [RESTful API Tutorial](https://restfulapi.net/) for comprehensive information about REST APIs and how to use them.

## Authentication

[Section titled “Authentication”](#authentication)

To ensure only authorized users have access to the Aembit REST API, Aembit enforces authentication to validate and verify whether a developer should be allowed to make requests. To provide flexibility to users, several types of authentication methods are supported, depending on the type of API client you want to implement.

The sections below describe the available authentication methods.

### Using the Session Access Token

[Section titled “Using the Session Access Token”](#using-the-session-access-token)

One way you may authenticate to the Aembit API is to use a session specific access token in your API requests. When you sign into your tenant, you will be assigned an access token for this session.

To locate the access token, follow the steps below.

1. Log into your tenant. The Dashboard page appears.
2. In the bottom-left corner of the Dashboard page, hover over your name. Notice that a **Profile** link appears. ![Dashboard Page](https://docs.aembit.io/_astro/authentication_main_page_user_profile.P0nVL3xB_Z17WVKB.webp)
3. Click on the **Profile** link. The User Profile dialog window appears.
4. In the API Access section of the dialog window, copy the values in the **Access Token** and **API Base Url** fields. ![User Profile Dialog Window - API Access](https://docs.aembit.io/_astro/authentication_user_profile_api_access.DAHTUZ2z_Z5WWxH.webp)

You may use the **Access Token** and **API Base Url** values in your API requests, as shown below in the [REST API Request Structure](#rest-api-request-structure) section.

### Using Aembit Native Authentication

[Section titled “Using Aembit Native Authentication”](#using-aembit-native-authentication)

Aembit also supports authentication to the Aembit API using a native authentication capability which utilizes OIDC (Open ID Connect tokens) ID Tokens. This capability requires configuring your Aembit Tenant with the appropriate components as follows:

* Client Workload
  * For the source of your API requests (e.g. GitHub Actions, GitLab Jobs, etc.)
* Trust Provider
  * To authenticate using cryptographic verification
* Credential Provider
  * Using the Aembit Access Token type and a Role with permissions to the appropriate entities
* Server Workload
  * Referencing the tenant-specific Aembit API hostname
* Access Policy
  * Using the access entities above enables the configured Client Workload access to the Aembit API

## REST API Request Structure

[Section titled “REST API Request Structure”](#rest-api-request-structure)

Making a REST API call to a server requires adhering to strict syntax and formatting guidelines for the server to process the request correctly. REST APIs rely on clients and users following prescribed URL structures and HTTP methods to ensure proper request handling and response generation. While there’s flexibility in data formats, the core structure of a REST API request remains consistent to perform specific actions and tasks in a single request.

The structure of a REST API call typically consists of the following elements:

* HTTP Method (REST Verb)

* REST API Url

  * Base Url
  * Version
  * Resource

* HTTP Authorization Header Access Token

### HTTP Method (REST Verb)

[Section titled “HTTP Method (REST Verb)”](#http-method-rest-verb)

When you want to make an API request, there are several types of requests (verbs) you can use, depending on what task you are trying to perform.

REST uses the following verbs:

* **GET** - retrieves information from one more resources
* **POST** - creates a new resource
* **PUT** - updates an existing resource
* **PATCH** - partially updates an existing resource
* **DELETE** - deletes a resource

### Base URL

[Section titled “Base URL”](#base-url)

The Base Url value is the standard HTTP address where requests are sent to the API server.

For the Aembit API, this will be the **API Base Url** from your Aembit Tenant and can be retrieved using the steps below:

1. Log into your Aembit Tenant.

2. On the main Dashboard page, hover over your name in the bottom-left corner and click on **Profile**. A User Profile dialog window appears. ![User Profile Dialog Window](https://docs.aembit.io/_astro/dashboard_api_access_fields.D94sVqN7_Vdyjq.webp)

3. In the **API Access** section, copy the values in the **API Token** and **API Base Url** fields. You will need these values in your API client to make requests to the server.

### Versioning

[Section titled “Versioning”](#versioning)

Every public API, including the Aembit API, includes a **Version** that distinguishes it from other API versions. If an organization manages multiple versions of an API (e.g. v1, v2, v3, etc.), each version often includes different features and functions. Depending on the API version used in the request, you may have access to different features.

To make it easier for users to make calls to the correct API version, the version number is included the API request. So, for example, in the Aembit API, a request with the version number looks like the following example:

`/api/v1/users`

The `v1` in the URL specifies that the request is for the `v1` version of the Aembit API. By adding the version number in the URL, this tells the server that you want to access resources for that specific version of the API.

### Resource

[Section titled “Resource”](#resource)

The `resource` value is the specific resource being called. For example, `server-workloads` and `client-workloads` are resources.

### Access Token

[Section titled “Access Token”](#access-token)

The Access Token value is a unique identifier that can be included in the API request, enabling Aembit APIs to identify the requestor and verify the enabled API permissions.

Note

Aembit requires Authorization Header Bearer (access) tokens to be used when making API calls and does not support long lived credentials.

### Resource Set (optional)

[Section titled “Resource Set (optional)”](#resource-set-optional)

Aembit supports an optional feature where customers can segment their tenant into multiple isolated Resource Sets that do not interact. To manage access entities in custom Resource Sets, an additional HTTP Header value is required, `X-Aembit-ResourceSet`.

Note

If the `X-Aembit-ResourceSet` HTTP header is not specified, then the Aembit API will operate against the Default Resource Set (identified as `ffffffff-ffff-ffff-ffff-ffffffffffff`).

### REST API Request Example

[Section titled “REST API Request Example”](#rest-api-request-example)

A typical Aembit API request should look similar to the `curl` examples shown below:

```bash
curl -X GET -L 'https://tenant.aembit.io/api/v1/server-workloads' -H 'Authorization: Bearer <TOKEN>'


curl -X GET -L 'https://tenant.aembit.io/api/v1/server-workloads' -H 'Authorization: Bearer <TOKEN>' -H 'X-Aembit-ResourceSet: ffffffff-ffff-ffff-ffff-ffffffffffff'
```

Where:

* `GET` is the type of request being made
* `https://tenant.aembit.io/api` is the Base URL
* `v1` is the API version
* `server-workloads` is the resource being called

## REST API Response

[Section titled “REST API Response”](#rest-api-response)

Every REST API response includes a HTTP Status Code and a response body. Successful responses will typically include a response body with the `Content-Type: application/json` and associated, structured data.

### Status Codes

[Section titled “Status Codes”](#status-codes)

Whenever you use the Aembit REST API, you will receive a HTTP status code when your request has been processed. The type of status code you receive depends on whether your request was successful or not.

The Aembit API uses standard HTTP Status Codes to denote whether a request has been successfully processed. Generally, you will see three types of status codes returned after you make a request.

* **2XX Codes** - If you request is successful, you will receive a 2XX error response code (e.g. 200, 201, etc).

* **4xx Codes** - If your request is not successful, and there is an client error in your request, you will receive a 4XX error response code (e.g. 401, 403, 404, etc).

* **5xx Codes** - If you request is not successful, and there is a server error, you will receive a 5XX error response code (e.g. 500, 502, 503, etc).

Note

For a full list of HTTP Status Codes, please see the [REST API Tutorial Status Codes](https://www.restapitutorial.com/httpstatuscodes) page.

## OpenAPI YAML

[Section titled “OpenAPI YAML”](#openapi-yaml)

Specific details about the Aembit API are available in the sections on the left and grouped based on the associated Aembit entities or features. However, in some cases, you may want the original OpenAPI document for importing into a code generator which can be downloaded with the link below.

[Download Aembit API OpenAPI Document](https://docs.aembit.io/cloud.yaml)
