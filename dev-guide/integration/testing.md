---
type: troubleshooting
title: "Test and debug your integration"
description: "Verify credential delivery end to end for the Agent Proxy, Edge SDK, Aembit CLI, and Edge API integration paths"
resource: https://docs.aembit.io/dev-guide/integration/testing/
interface: sdk
tags: ["integration"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Test and debug your integration

After you integrate your application through any of the [integration paths](../overview.md), follow the steps on this page to verify that credentials flow end to end. A working integration means Aembit authorizes the request, your application receives a valid credential, and the target Server Workload accepts it.

Whichever path you chose, your Aembit Tenant records the full authorization flow for every access request that reaches it, so verification starts in the Tenant and ends at your application. Each path also produces its own local evidence, which the per-path sections on this page cover.

## Check authorization events in your Aembit Tenant

Every integration path produces the same authorization events in your Aembit Tenant, so check them first when access fails. To view them, click **Reporting** in the left sidebar of your Tenant, which displays the **Access Authorization Events** page. The events trace each request through four event types: Access Request, Access Authorization, Access Discovery, and Access Credential.

Each authorization event records the identified Client Workload, Server Workload, and whether Aembit granted or denied access, together with the reason for a denial.

A denial reason such as an attestation failure tells you the Access Policy, the Trust Provider, or another Access Policy component is the problem rather than your code. See [Access Authorization Events](../../user-guide/audit-report/access-authorization-events.md) for each event type, its fields, and worked failure examples.

## Verify credential delivery

The verification steps differ by path, because each path puts the credential-handling logic in a different place. Select your integration path:

* Agent Proxy

  Agent Proxy intercepts your application’s requests transparently, so verify that the interception happened and that the injected credential worked.

  1. Check the Agent Proxy logs for credential events, which confirm Agent Proxy intercepts your application’s requests.

     **Linux (systemd)**

     ```shell
     sudo journalctl --namespace aembit_agent_proxy | grep -i "credential"
     ```

     **Kubernetes**

     ```shell
     # Standalone deployment
     kubectl logs <agent-proxy-pod> -n <namespace> -f


     # Sidecar injection (Agent Proxy runs as a container in your application pod)
     kubectl logs <pod> -n <namespace> -c aembit-agent-proxy -f
     ```

     Log lines that reference GetCredentials, credential injection, or authentication confirm the interception. Check the Agent Proxy logs specifically, because the Agent Controller logs cover registration and Access Policy sync rather than credential interception.

  2. Confirm your application receives a valid credential by logging the result of its normal authentication call. For example, an application that requests an OAuth token logs the intercepted request’s result:

     ```python
     token_response = requests.post(
         'https://oauth-provider.com/token',
         data={
             'grant_type': 'client_credentials',
             'client_id': 'your-app-id',
             'client_secret': 'placeholder-client-secret',  # Aembit replaces this
             'scope': 'api.read'
         }
     )


     if token_response.ok:
         logger.info("Received access token, expires in %s seconds", token_response.json().get('expires_in'))
     else:
         logger.error("Token request failed: %s", token_response.text)
     ```

     A valid access token, an API response in the 200-299 range, or an established database connection confirms delivery, depending on your target service.

  3. Confirm the target service accepts the credential by calling a protected resource. A response in the 200-299 range with no `401 Unauthorized` or `403 Forbidden` errors completes the verification.

* Edge SDK

  The Edge SDK retrieves credentials in-process, so failures surface as errors from the SDK calls your code makes.

  1. Supply a logger when you construct the client, because the SDK stays silent by default. Any object with `debug`, `info`, `warn`, and `error` methods works, and the logger reveals request lifecycles, token caching, and errors.

     **edge-client.ts**

     ```typescript
     import { EdgeClient, trustProviders } from "@aembit/edge-sdk";


     const client = new EdgeClient({
       baseUrl: "https://<tenantId>.aembit.io",
       clientId: "<your-edge-sdk-client-id>",
       trustProvider: trustProviders.awsMetadataService(),
       logger: console,
     });
     ```

  2. Authenticate and retrieve a credential, and branch on the `kind` the SDK attaches to every error:

     * `trust_provider` or `auth` - The identity evidence from the environment doesn’t satisfy the Trust Provider, so compare the two using the [Edge API authentication contexts](../api/edge/auth/overview.md).
     * `credential` - Aembit denied retrieval, so check the [authorization events](#check-authorization-events-in-your-aembit-tenant) for the Access Policy denial reason.
     * `transport` - The request never reached Aembit, so check network egress from your workload to your Tenant.

  3. Confirm the target service accepts the credential your code attaches to the request, in the format the target expects, such as a `Bearer` header or a database password.

  The [SDK repository](https://github.com/Aembit/edge-sdks) carries language-specific logging and error-handling examples.

* Aembit CLI

  The `aembit credentials get` command retrieves the credential directly, so its output and exit code tell you whether retrieval succeeded.

  1. Run the command on its own before wiring it into a script:

     ```shell
     aembit credentials get \
       --server-workload-host api.example.com \
       --server-workload-port 443 \
       --client-id <your-edge-sdk-client-id>
     ```

     The command prints the credential on success and an error on failure, and its exit code tells your script which happened.

  2. When retrieval fails, match the error against [Troubleshooting the Aembit CLI](../cli/troubleshooting.md), which covers Access Policy matching, Credential Provider, and connectivity errors.

  3. Confirm the target service accepts the credential your script passes it.

  > **Environment variable and command option priority**
  >
  > You can configure the Aembit CLI using both environment variables and command options. Command options take precedence, overriding any corresponding environment variables.
  >
  > For example, if you’ve set a value with the `--client-id` option, Aembit CLI uses that over the `AEMBIT_CLIENT_ID` environment variable. This lets you establish a default configuration with environment variables and override specific settings for individual commands as needed.

* Edge API

  The Edge API makes each stage an explicit HTTP call, so the response status of each call locates the failure.

  1. Authenticate by sending your environment’s identity evidence, such as an OIDC identity token or AWS instance metadata, to the `/edge/v1/auth` endpoint, and expect a `200` response with an `accessToken`. A `401` here means the identity evidence doesn’t satisfy the Trust Provider, so check the [authentication context](../api/edge/auth/overview.md) for your environment and the Edge SDK Client ID you passed. Pass the identity evidence in the request body, because setting it as an `Authorization` header also returns `401`.

  2. Retrieve the credential from the `/edge/v1/credentials` endpoint with the `accessToken` as a `Bearer` header, and expect a `200` response with the credential data. A failure here means Aembit denied retrieval, so check the [authorization events](#check-authorization-events-in-your-aembit-tenant) for the Access Policy denial reason.

  3. Confirm the target service accepts the credential your code attaches to the request.

  The [endpoint reference](../api/edge/endpoints/overview.md) covers request and response details, and the [Edge API quickstart](../api/edge/quickstart-edge.md#common-errors-and-solutions) lists common errors with solutions.

## Troubleshoot common failures

The most common failures also differ by path. Select your integration path:

* Agent Proxy

  * **Your client library reports invalid credentials** - Verify Agent Proxy is running with `systemctl status aembit_agent_proxy`, check its logs for interception activity, and confirm the Access Policy is active and correctly configured.
  * **The placeholder value appears in the target service’s logs** - The interception isn’t happening, so verify the `HTTP_PROXY` environment variables for proxy-based interception and configure [TLS Decrypt](../../user-guide/deploy-install/advanced-options/tls-decrypt/overview.md) for HTTPS targets.
  * **The application works locally but fails when deployed** - Confirm the deployed environment doesn’t set the real secret in an environment variable, runs Agent Proxy and Agent Controller, and can reach Aembit Cloud.

  > **HTTP proxy configuration**
  >
  > If your network routes outbound traffic through an HTTP proxy, configure the `HTTP_PROXY`, `HTTPS_PROXY`, and `NO_PROXY` environment variables for Agent Controller. See [Agent Controller environment variables](../../reference/edge-components/edge-component-env-vars.md#http_proxy) for details.

* Edge SDK

  * **Attestation fails on every call** - The workload’s environment doesn’t present evidence the Trust Provider can verify, so compare the Trust Provider’s match rules against the [evidence your environment provides](../api/edge/auth/overview.md).
  * **Authentication succeeds but retrieval is denied** - No active Access Policy permits the Client Workload to reach the Server Workload, so check the Access Policy and the authorization events.
  * **Calls fail before reaching Aembit** - `transport` errors point to network egress, so confirm the workload can reach your Tenant’s Edge API base URL.

* Aembit CLI

  [Troubleshooting the Aembit CLI](../cli/troubleshooting.md) covers the common failures with solutions, including:

  * No output when using `--credential-names`
  * `TOKEN` credential mismatch errors
  * Access Policy matching errors
  * Invalid `client_id` errors
  * Can’t-connect-to-cloud errors

* Edge API

  * **`401 Unauthorized`** - The identity evidence is invalid or expired (CI/CD OIDC tokens expire after 1 hour), the Edge SDK Client ID is wrong, or the request passes the identity evidence as an `Authorization` header instead of in the body.
  * **`400 Bad Request`** - The `client` object is missing required attestation data, or the `credentialType` is unsupported.
  * **`500 Internal Server Error`** - The Credential Provider configuration or the connection to the target service is the problem, so check the provider and retry after a brief delay.

  The [Edge API quickstart](../api/edge/quickstart-edge.md#common-errors-and-solutions) shows each error response with causes and solutions.

## Related resources

* **[Server Workload troubleshooting](../../user-guide/access-policies/server-workloads/troubleshooting.md)** - Common issues and solutions beyond integration testing
* **[Local development](local-development.md)** - Get credentials while developing on your own machine
* **[Integrate through Agent Proxy](agent-proxy.md)** - The developer-side procedure, from placeholder credential to verified request
