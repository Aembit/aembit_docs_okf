---
type: how-to
title: "Local development"
description: "Three ways to get Aembit-managed credentials while developing on your own machine"
resource: https://docs.aembit.io/dev-guide/integration/local-development/
interface: sdk
tags: ["integration"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Local development

For local development, you have three options to get credentials from Aembit, and they differ in how close each one is to your production setup.

## Option 1: Aembit CLI credential injection

Use the Aembit CLI to retrieve credentials and inject them into environment variables. This is useful for local development without running the full Agent infrastructure.

```shell
# Get credentials and export to environment variable
export MY_API_KEY=$(aembit credentials get \
  --server-workload-host api.example.com \
  --server-workload-port 443 \
  --client-id YOUR_CLIENT_ID)


# Run your application with the credential
python my_app.py
```

The CLI authenticates through a Trust Provider and needs the Edge SDK Client ID from that Trust Provider, so check the [supported Trust Providers](../cli/overview.md#supported-trust-providers) before you start. See [Getting credentials with Aembit CLI](../cli/usage/get-credentials.md) for detailed setup instructions.

## Option 2: Run Aembit Agent Proxy locally

Install and run both Aembit Agent Controller and Agent Proxy on your development machine for end-to-end credential injection that matches production behavior. This is the highest-fidelity option when your deployed workload [integrates through Agent Proxy](agent-proxy.md).

### Set up a local Agent Proxy

1. Install Agent Controller and Agent Proxy on your development machine, following [About Agent Controller](../../user-guide/deploy-install/about-agent-controller.md).

2. Configure Agent Proxy to connect to your Aembit Tenant.

3. In your Aembit Tenant, create the Access Policy components that authorize your local application:

   * A Server Workload that represents the target service
   * A Client Workload that identifies your local application
   * A Trust Provider that can verify your development machine’s identity evidence
   * A Credential Provider that supplies the credential for the target

4. Create and activate an Access Policy that ties the components together, following [Create an Access Policy](../../user-guide/access-policies/create-access-policy.md), because Aembit injects a credential only when an active Access Policy permits the access.

5. Run your application locally, and Agent Proxy intercepts its traffic as it does in production.

## Option 3: Call the Edge SDK from your application

If your application uses an [Edge SDK](../sdk/edge/overview.md), the same code runs on your development machine, because the SDK retrieves credentials in-process without any local Aembit components. The SDK authenticates with identity evidence from the environment it runs in. Your development machine therefore needs to present evidence that a Trust Provider you configured can verify, the same requirement the CLI has. See the [Edge API authentication contexts](../api/edge/auth/overview.md) for the evidence each environment provides, and the [SDK repository](https://github.com/Aembit/edge-sdks) for language-specific setup and runnable examples.

## Related resources

* **[Test and debug your integration](testing.md)** - Verify credential delivery end to end
* **[Aembit CLI](../cli/overview.md)** - Supported platforms, Trust Providers, and Credential Providers
* **[Edge SDK overview](../sdk/edge/overview.md)** - What the SDKs handle for you
