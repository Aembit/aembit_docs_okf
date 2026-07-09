---
type: how-to
title: "Developer Integration with Server Workloads"
description: "SDK integration patterns, placeholder credentials, and testing procedures for Server Workload integrations"
resource: https://docs.aembit.io/user-guide/access-policies/server-workloads/developer-integration/
interface: web-ui
tags: [server-workload, access-policy]
timestamp: 2025-11-26T13:16:45-08:00
type_inferred: true
---

# Developer Integration with Server Workloads

This guide shows developers how to integrate their application code with Aembit Server Workloads**Server Workload**: Server Workloads represent target services, APIs, databases, or applications that receive and respond to access requests from Client Workloads.[Learn more](../../../get-started/concepts/server-workloads.md).

**No application code changes required** - Aembit intercepts authentication requests transparently. However, you need to understand how to initialize SDKs and test your integration.

## Understanding placeholder credentials

[Section titled “Understanding placeholder credentials”](#understanding-placeholder-credentials)

When using SDKs or libraries to connect to services, many require credentials during initialization even though Aembit provides the actual credentials at runtime.

### What are placeholder credentials?

[Section titled “What are placeholder credentials?”](#what-are-placeholder-credentials)

A **placeholder credential** is a placeholder value used only for SDK initialization. The Aembit Agent Proxy intercepts authentication requests and replaces placeholder values with real, dynamically generated credentials before they reach the target service.

**The placeholder value never leaves your environment.**

**Examples of valid placeholders** -

* `'placeholder-client-secret'`
* `'aembit-managed'`
* `'dummy-value-12345'`
* Any non-empty string that satisfies SDK validation

### Why use placeholders?

[Section titled “Why use placeholders?”](#why-use-placeholders)

SDKs validate that required credential fields are present during initialization. Without placeholders, SDKs throw errors like:

```plaintext
ValueError: client_secret is required
```

Placeholders satisfy SDK validation while allowing Aembit to manage the actual credentials securely.

## Integration pattern

[Section titled “Integration pattern”](#integration-pattern)

Most authentication libraries follow this pattern. Here’s a generic example showing before and after Aembit:

### Before Aembit (managing secrets manually)

[Section titled “Before Aembit (managing secrets manually)”](#before-aembit-managing-secrets-manually)

```python
import os
import requests


# Secret loaded from environment variable or secret manager
client_secret = os.environ.get('CLIENT_SECRET')  # ← Security risk: secret in env


# Make OAuth token request
token_response = requests.post(
    'https://oauth-provider.com/token',
    data={
        'grant_type': 'client_credentials',
        'client_id': 'your-app-id',
        'client_secret': client_secret,  # ← Real secret sent
        'scope': 'api.read api.write'
    }
)


access_token = token_response.json()['access_token']


# Use access token for API calls
api_response = requests.get(
    'https://api.example.com/resource',
    headers={'Authorization': f'Bearer {access_token}'}
)
```

**Problems with this approach** -

* Secret stored in environment variable (risk of leakage)
* Manual rotation required (downtime, code changes)
* Secret visible in logs if request fails
* No centralized credential management

### With Aembit (no secret management required)

[Section titled “With Aembit (no secret management required)”](#with-aembit-no-secret-management-required)

```python
import requests


# Use placeholder credential - Aembit replaces this at runtime
client_secret = 'placeholder-client-secret'  # ← Aembit intercepts and replaces


# Same OAuth token request - Aembit handles credentials
token_response = requests.post(
    'https://oauth-provider.com/token',
    data={
        'grant_type': 'client_credentials',
        'client_id': 'your-app-id',
        'client_secret': client_secret,  # ← Placeholder never reaches OAuth provider
        'scope': 'api.read api.write'
    }
)


access_token = token_response.json()['access_token']  # ← You get a valid token


# Use access token for API calls (unchanged)
api_response = requests.get(
    'https://api.example.com/resource',
    headers={'Authorization': f'Bearer {access_token}'}
)
```

**Key changes** -

* ✅ No environment variables or secret managers needed
* ✅ No secret rotation logic in application code
* ✅ Placeholder credential never reaches the target service (Aembit intercepts)
* ✅ Centralized credential management in Aembit

## Service-specific SDK resources

[Section titled “Service-specific SDK resources”](#service-specific-sdk-resources)

When integrating with your specific service, use these resources for SDK-specific guidance:

### OAuth-based services

[Section titled “OAuth-based services”](#oauth-based-services)

**Entra ID (Microsoft Identity Platform)**

* [Entra ID Server Workload guide](guides/entra-id.md) - Aembit configuration
* [Microsoft Authentication Library (MSAL) Python documentation](https://learn.microsoft.com/en-us/entra/msal/python/) - Official Python SDK
* [Microsoft Authentication Library (MSAL) Node.js documentation](https://learn.microsoft.com/en-us/entra/msal/node/) - Official Node.js SDK

**Salesforce**

* [Salesforce Server Workload guide](guides/salesforce-rest.md) - Aembit configuration
* [simple-salesforce library](https://github.com/simple-salesforce/simple-salesforce) - Python SDK
* [JSforce documentation](https://jsforce.github.io/) - Node.js SDK

**GitHub**

* [GitHub Server Workload guide](guides/github-rest.md) - Aembit configuration (OAuth mode)
* [Octokit documentation](https://github.com/octokit) - Official SDK (multiple languages)

### API key services

[Section titled “API key services”](#api-key-services)

**Okta**

* [Okta Server Workload guide](guides/okta.md) - Aembit configuration
* [Okta Python SDK](https://github.com/okta/okta-sdk-python) - Official Python SDK
* [Okta Node.js SDK](https://github.com/okta/okta-sdk-nodejs) - Official Node.js SDK

**Claude (Anthropic)**

* [Claude Server Workload guide](guides/claude.md) - Aembit configuration
* [Anthropic Python SDK](https://github.com/anthropics/anthropic-sdk-python) - Official Python SDK
* [Anthropic TypeScript SDK](https://github.com/anthropics/anthropic-sdk-typescript) - Official TypeScript SDK

**OpenAI**

* [OpenAI Server Workload guide](guides/openai.md) - Aembit configuration
* [OpenAI Python library](https://github.com/openai/openai-python) - Official Python SDK
* [OpenAI Node.js library](https://github.com/openai/openai-node) - Official Node.js SDK

### Database services

[Section titled “Database services”](#database-services)

**MySQL**

* [AWS MySQL guide](guides/aws-mysql.md) - Aembit configuration for RDS
* [Local MySQL guide](guides/local-mysql.md) - Aembit configuration for local/on-prem
* [mysql-connector-python](https://dev.mysql.com/doc/connector-python/en/) - Official Python driver
* [mysql2](https://github.com/sidorares/node-mysql2) - Node.js driver

**PostgreSQL**

* [AWS Postgres guide](guides/aws-postgres.md) - Aembit configuration for RDS
* [Local Postgres guide](guides/local-postgres.md) - Aembit configuration for local/on-prem
* [psycopg3](https://www.psycopg.org/psycopg3/) - Official Python driver
* [node-postgres (pg)](https://node-postgres.com/) - Node.js driver

### Cloud provider services

[Section titled “Cloud provider services”](#cloud-provider-services)

**AWS**

* [AWS Cloud guide](guides/aws-cloud.md) - Aembit configuration for AWS APIs
* [Boto3](https://boto3.amazonaws.com/v1/documentation/api/latest/index.html) - Official Python SDK for AWS
* [AWS SDK for JavaScript](https://docs.aws.amazon.com/AWSJavaScriptSDK/v3/latest/) - Official Node.js SDK

**Important** For S3 uploads, Aembit Agent Proxy doesn’t support all AWS signing methods out of the box. -

Configure your SDK to use “Unsigned Payload” mode or similar options as documented in the [Support Matrix](../../../reference/support-matrix.md).

## Testing your integration

[Section titled “Testing your integration”](#testing-your-integration)

After deploying your application with Aembit integration, follow these steps to verify everything works correctly.

### Step 1: Verify Aembit intercepts requests

[Section titled “Step 1: Verify Aembit intercepts requests”](#step-1-verify-aembit-intercepts-requests)

When debugging runtime credential flow (what developers care about during integration testing), you must check the Agent Proxy logs. The Agent Controller logs don’t show the actual credential interception events that verify your application integration is working.

Check Aembit Agent Proxy logs for successful credential injection:

**Linux (systemd)** -

```shell
# Monitor logs for credential-related events
sudo journalctl --namespace aembit_agent_proxy | grep -i "credential"


# For time-bounded logs:
sudo journalctl --namespace aembit_agent_proxy --since "YYYY-MM-DD HH:MM:SS" --until "YYYY-MM-DD HH:MM:SS"
```

**Docker/Kubernetes** -

```shell
# Find the Agent Proxy pod name
kubectl get pods -n <namespace> | grep agent-proxy


# View Agent Proxy logs (standalone deployment)
kubectl logs <agent-proxy-pod> -n <namespace> -f


# Example (standalone):
kubectl logs aembit-agent-proxy-5d8f7b9c4-xk8mh -n aembit -f


# If using sidecar injection (Agent Proxy runs as container in application pod):
kubectl logs <pod> -n <namespace> -c aembit-agent-proxy -f
```

**Expected output** - Look for log entries referencing credential requests, credential injection, or authentication events. Log lines may reference GetCredentials, credential injection, or authentication.

### Step 2: Verify application receives valid credentials

[Section titled “Step 2: Verify application receives valid credentials”](#step-2-verify-application-receives-valid-credentials)

Add debug logging to your application to confirm credential flow:

**Python example** -

```python
import logging


logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)


# Attempt authentication
result = client.authenticate()


# Verify response contains access token or credentials
if 'access_token' in result:
    logger.info("✓ Successfully received access token from service")
    logger.info(f"Token expires in {result.get('expires_in')} seconds")
else:
    logger.error("✗ Token acquisition failed")
    logger.error(f"Error: {result.get('error')}")
    logger.error(f"Description: {result.get('error_description')}")
```

**Expected output** -

* ✅ “Successfully received access token” (OAuth services)
* ✅ API response with status code 200-299 (API key services)
* ✅ Database connection established (database services)

### Step 3: Verify application can access protected resources

[Section titled “Step 3: Verify application can access protected resources”](#step-3-verify-application-can-access-protected-resources)

Test authentication to your target API or service:

**OAuth services** -

```python
import requests


# Use the acquired token to call protected API
headers = {'Authorization': f'Bearer {access_token}'}
response = requests.get('https://api.example.com/resource', headers=headers)


if response.status_code == 200:
    print("✓ Successfully authenticated to protected resource")
    print(f"Response: {response.json()}")
else:
    print(f"✗ Authentication failed: HTTP {response.status_code}")
    print(f"Error: {response.text}")
```

**Database services** -

```python
import mysql.connector


# Test database query
try:
    connection = mysql.connector.connect(
        host='database.example.com',
        user='placeholder-username',  # Aembit replaces
        password='placeholder-password',  # Aembit replaces
        database='mydb'
    )
    cursor = connection.cursor()
    cursor.execute("SELECT 1")
    result = cursor.fetchone()
    print("✓ Database query successful")
    cursor.close()
    connection.close()
except Exception as e:
    print(f"✗ Database connection failed: {e}")
```

**Expected results** -

* ✅ Aembit logs show request interception
* ✅ Application receives valid credentials (access token, API response, database connection)
* ✅ API calls with credentials succeed (HTTP 200-299)
* ✅ No 401 Unauthorized or 403 Forbidden errors

## Local development

[Section titled “Local development”](#local-development)

For local development, you have two options to get credentials from Aembit:

### Option 1: Aembit CLI credential injection

[Section titled “Option 1: Aembit CLI credential injection”](#option-1-aembit-cli-credential-injection)

Use the Aembit CLI to retrieve credentials and inject them into environment variables. This is useful for local development without running the full Agent infrastructure.

```shell
# Get credentials and export to environment variable
export MY_API_KEY=$(aembit credentials get \
  --server-workload-host api.example.com \
  --server-workload-port 443 \
  --edge-sdk-client-id YOUR_CLIENT_ID)


# Run your application with the credential
python my_app.py
```

See [Getting credentials with Aembit CLI](../../../cli-guide/usage/get-credentials.md) for detailed setup instructions.

### Option 2: Run Aembit Agent Proxy locally

[Section titled “Option 2: Run Aembit Agent Proxy locally”](#option-2-run-aembit-agent-proxy-locally)

Install and run both Aembit Agent Controller and Agent Proxy on your development machine for end-to-end credential injection that matches production behavior.

**Setup** -

1. Install Agent Controller and Agent Proxy: See [Agent Controller installation guide](../../deploy-install/about-agent-controller.md)
2. Configure Agent Proxy to point to the correct environment
3. Configure local Server Workload in Aembit Tenant
4. Run application locally - Agent Proxy intercepts traffic just like production

See [About Agent Controller](../../deploy-install/about-agent-controller.md) for installation details

## Common integration patterns

[Section titled “Common integration patterns”](#common-integration-patterns)

The following sections show common integration patterns for different authentication methods.

### Pattern 1: OAuth SDK initialization

[Section titled “Pattern 1: OAuth SDK initialization”](#pattern-1-oauth-sdk-initialization)

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


# Use token for API calls
api_client.call_api(access_token=token)
```

**Key points** -

* Placeholder in `client_secret` parameter
* SDK handles token request automatically
* Aembit intercepts `POST /token` request
* SDK receives valid access token

### Pattern 2: API key in headers

[Section titled “Pattern 2: API key in headers”](#pattern-2-api-key-in-headers)

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

**Key points** -

* No API key in application code
* Aembit injects header transparently
* Application sees normal API responses

### Pattern 3: Database connection

[Section titled “Pattern 3: Database connection”](#pattern-3-database-connection)

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

**Key points** -

* Placeholders in `user` and `password` parameters
* Aembit intercepts connection request
* Driver receives valid connection

## Troubleshooting integration issues

[Section titled “Troubleshooting integration issues”](#troubleshooting-integration-issues)

**Understanding component roles** -

* **Agent Proxy**: Handles runtime traffic interception and credential injection. Check Agent Proxy logs for credential-related issues.
* **Agent Controller**: Handles registration, policy sync, and orchestration. Check Agent Controller logs for registration or policy sync issues.

**Problem: SDK throws “invalid credentials” error**

**Solution** -

* Verify Agent Proxy is running: `systemctl status aembit_agent_proxy` (Linux)
* Check Agent Proxy logs for interception activity
* Check the associated Access Policy is active and that you have configured it correctly
* Ensure placeholder credential matches expected format
* See [Troubleshooting Guide](troubleshooting.md) for common issues

**Problem: Placeholder credential appears in service logs**

**Solution** -

* Verify you set `HTTP_PROXY` environment variables (for proxy-based interception)
* Check [TLS Decrypt](../../deploy-install/advanced-options/tls-decrypt/overview.md) configuration (required for HTTPS services)
* Verify Agent Proxy is intercepting traffic (check logs)

**Problem: Application works locally but fails in deployed environment**

**Solution** -

* Ensure `CLIENT_SECRET` environment variable isn’t set in deployed environment (should use placeholder)
* Check network connectivity from deployed environment to Aembit Cloud
* Verify Agent Proxy and Agent Controller are running in your deployed environment

## Related resources

[Section titled “Related resources”](#related-resources)

* **[Architecture Patterns](architecture-patterns.md)** - How different authentication methods work
* **[Troubleshooting Guide](troubleshooting.md)** - Common issues and solutions
* **[Server Workload Guides](guides/overview.md)** - Service-specific configuration
* **[Agent Controller](../../deploy-install/about-agent-controller.md)** - Understanding the Agent Controller
