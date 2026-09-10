---
type: reference
title: "Client library patterns for Agent Proxy"
description: "Where the placeholder credential goes for OAuth SDKs, API key headers, and database drivers behind Agent Proxy"
resource: https://docs.aembit.io/dev-guide/integration/client-library-patterns/
interface: sdk
tags: ["integration"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Client library patterns for Agent Proxy

Behind Agent Proxy, the only application-side integration work is placing a placeholder credential where your client library requires one. This page explains placeholder credentials, shows where the placeholder goes for the three common library types, and links the official SDK documentation for the services Aembit documents. For the integration procedure itself, see [Integrate through Agent Proxy](agent-proxy.md).

## Understanding placeholder credentials

A **placeholder credential** is a stand-in value used only for client library initialization. Agent Proxy intercepts authentication requests and replaces placeholder values with real, dynamically generated credentials before they reach the target Server Workload. The placeholder never reaches the target service.

Any non-empty string that satisfies your library’s validation works as a placeholder, such as `'placeholder-client-secret'`, `'aembit-managed'`, or `'dummy-value-12345'`.

Libraries need a placeholder because they validate that required credential fields are present during initialization, and without a value they throw errors like:

```plaintext
ValueError: client_secret is required
```

The placeholder satisfies that validation while Aembit manages the actual credential.

## OAuth SDK initialization

Most OAuth SDKs follow this initialization pattern:

```python
from some_oauth_library import OAuthClient


# Initialize with placeholder
client = OAuthClient(
    client_id='your-client-id',
    client_secret='placeholder-client-secret',  # ← Aembit replaces
    token_url='https://oauth-provider.com/token'
)


# Acquire token - Aembit intercepts this request
token = client.get_access_token(scopes=['api.read'])


# Use the token with your service's API client
api_client.call_api(access_token=token)
```

The placeholder goes in the `client_secret` parameter, and the SDK sends the token request automatically. Aembit intercepts the `POST /token` request, so the SDK receives a valid access token.

## API key in headers

API key libraries typically set headers:

```python
import requests


# Aembit injects API key into Authorization header automatically
# Application code doesn't include the key at all
response = requests.get(
    'https://api.example.com/resource',
    # No Authorization header needed - Aembit adds it
)
```

The application code carries no API key, because Aembit injects the header transparently, and the application sees normal API responses.

## Database connection

Database drivers use connection parameters:

```python
import psycopg


# Placeholders in connection string
connection = psycopg.connect(
    "host=database.example.com "
    "port=5432 "
    "dbname=mydb "
    "user=placeholder-username "  # ← Aembit replaces
    "password=placeholder-password"  # ← Aembit replaces
)


# Use connection normally
cursor = connection.cursor()
cursor.execute("SELECT * FROM users")
```

The placeholders go in the `user` and `password` parameters, and Aembit intercepts the connection request, so the driver receives a valid connection.

## Service-specific SDK resources

When integrating with your specific service, use these resources for SDK-specific guidance:

### OAuth-based services

* **Entra ID (Microsoft Identity Platform)** - [Entra ID Server Workload guide](../../user-guide/access-policies/server-workloads/guides/entra-id.md) for the Aembit configuration, with the official [Microsoft Authentication Library (MSAL) for Python](https://learn.microsoft.com/en-us/entra/msal/python/) and [MSAL for Node.js](https://learn.microsoft.com/en-us/entra/msal/node/) SDK documentation
* **Salesforce** - [Salesforce Server Workload guide](../../user-guide/access-policies/server-workloads/guides/salesforce-rest.md) for the Aembit configuration, with the [simple-salesforce](https://github.com/simple-salesforce/simple-salesforce) Python SDK and [JSforce](https://jsforce.github.io/) Node.js SDK
* **GitHub** - [GitHub Server Workload guide](../../user-guide/access-policies/server-workloads/guides/github-rest.md) for the Aembit configuration in OAuth mode, with the official [Octokit](https://github.com/octokit) SDKs in multiple languages

### API key services

* **Okta** - [Okta Server Workload guide](../../user-guide/access-policies/server-workloads/guides/okta.md) for the Aembit configuration, with the official [Okta Python SDK](https://github.com/okta/okta-sdk-python) and [Okta Node.js SDK](https://github.com/okta/okta-sdk-nodejs)
* **Claude (Anthropic)** - [Claude Server Workload guide](../../user-guide/access-policies/server-workloads/guides/claude.md) for the Aembit configuration, with the official [Anthropic Python SDK](https://github.com/anthropics/anthropic-sdk-python) and [Anthropic TypeScript SDK](https://github.com/anthropics/anthropic-sdk-typescript)
* **OpenAI** - [OpenAI Server Workload guide](../../user-guide/access-policies/server-workloads/guides/openai.md) for the Aembit configuration, with the official [OpenAI Python library](https://github.com/openai/openai-python) and [OpenAI Node.js library](https://github.com/openai/openai-node)

### Database services

* **MySQL** - [AWS MySQL guide](../../user-guide/access-policies/server-workloads/guides/aws-mysql.md) for RDS and [Local MySQL guide](../../user-guide/access-policies/server-workloads/guides/local-mysql.md) for local or on-premises databases, with the official [mysql-connector-python](https://dev.mysql.com/doc/connector-python/en/) driver and the [mysql2](https://github.com/sidorares/node-mysql2) Node.js driver
* **PostgreSQL** - [AWS Postgres guide](../../user-guide/access-policies/server-workloads/guides/aws-postgres.md) for RDS and [Local Postgres guide](../../user-guide/access-policies/server-workloads/guides/local-postgres.md) for local or on-premises databases, with the official [psycopg3](https://www.psycopg.org/psycopg3/) driver and the [node-postgres (pg)](https://node-postgres.com/) Node.js driver

### Cloud provider services

* **AWS** - [AWS Cloud guide](../../user-guide/access-policies/server-workloads/guides/aws-cloud.md) for the Aembit configuration for AWS APIs, with the official [Boto3](https://boto3.amazonaws.com/v1/documentation/api/latest/index.html) Python SDK and [AWS SDK for JavaScript](https://docs.aws.amazon.com/AWSJavaScriptSDK/v3/latest/)

> **S3 uploads and AWS signing methods**
>
> For S3 uploads, Agent Proxy doesn’t support all AWS signing methods out of the box. Configure your SDK to use “Unsigned Payload” mode or similar options as documented in the [Support Matrix](../../reference/support-matrix.md).

## Next steps

* **[Integrate through Agent Proxy](agent-proxy.md)** - The developer-side procedure, from placeholder credential to verified request
* **[Test and debug your integration](testing.md)** - Verify interception, credential delivery, and access end to end
* **[Server Workload guides](../../user-guide/access-policies/server-workloads/guides/overview.md)** - Service-specific configuration
