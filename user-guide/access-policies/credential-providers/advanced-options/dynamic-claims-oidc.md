---
type: how-to
title: "Dynamic Claims for OIDC ID Token, JWT-SVID Token, and X.509-SVID Credential Providers"
description: "Learn how to use dynamic claims in OIDC ID Token, JWT-SVID Token, and X.509-SVID Credential Providers to extract and use values from workload identity"
resource: https://docs.aembit.io/user-guide/access-policies/credential-providers/advanced-options/dynamic-claims-oidc/
interface: web-ui
tags: ["advanced-option", "credential-provider", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Dynamic Claims for OIDC ID Token, JWT-SVID Token, and X.509-SVID Credential Providers

Dynamic claims in the [OIDC ID Token Credential Provider](../oidc-id-token.md), [JWT-SVID Token Credential Provider](../spiffe-jwt-svid.md), and [X.509-SVID Credential Provider](../spiffe-x509-svid.md) allow you to extract and use claims from an OIDC token in the credential data. This feature creates personalized and context-aware credentials that reflect the workload’s identity and attributes from their original OIDC token.

For the OIDC ID Token and JWT-SVID Token Credential Providers, dynamic expressions resolve in custom claims and the subject field. For the X.509-SVID Credential Provider, X.509 certificates do not carry arbitrary custom claims, so dynamic expressions resolve in the certificate’s **Subject** and **Spiffe ID** identity fields instead.

This functionality proves particularly useful in environments where OIDC tokens authenticate and authorize workloads, such as in cloud-native applications, CI/CD pipelines, or microservices architectures.

## How dynamic claims work

Dynamic claims operate with two main components:

1. **Template Definition** - Define dynamic values in Credential Provider configuration using expressions instead of static values
2. **Runtime Resolution** - Aembit collects the referenced information and replaces template variables with actual values

The process follows these steps:

1. You configure template expressions in your OIDC ID Token or JWT-SVID Token Credential Provider
2. When a workload makes a credential request, Aembit receives the incoming OIDC token
3. Aembit extracts the specified claims from the token using your template expressions
4. Aembit inserts the extracted values into the generated credential

## Dynamic claims syntax

Both the OIDC ID Token and JWT-SVID Token Credential Providers support dynamic claims using this syntax: `${expression}`

### Basic syntax patterns

* **OIDC Token Claims**: `${oidc.identityToken.decode.payload.claim_name}`
* **GitLab Token Claims**: `${gitlab.identityToken.decode.payload.claim_name}`
* **GitHub Token Claims**: `${github.identityToken.decode.payload.claim_name}`
* **Environment Variables**: `${os.environment.VARIABLE_NAME}`
* **Combined Values**: `${oidc.identityToken.decode.payload.user_login}_suffix`

### Common expression examples

| Expression                                                | Description                                  | Example Result         |
| --------------------------------------------------------- | -------------------------------------------- | ---------------------- |
| `${oidc.identityToken.decode.payload.user_email}`         | Extract workload email from OIDC token       | `workload@company.com` |
| `${oidc.identityToken.decode.payload.user_login}`         | Extract workload login/username              | `ci-workload`          |
| `${oidc.identityToken.decode.payload.groups}`             | Extract workload groups                      | `developers,admins`    |
| `${gitlab.identityToken.decode.payload.project_path}`     | Extract GitLab project path                  | `group/project`        |
| `${gitlab.identityToken.decode.payload.ref}`              | Extract GitLab branch/tag reference          | `main`                 |
| `${gitlab.identityToken.decode.payload.job_id}`           | Extract GitLab CI job ID                     | `123456789`            |
| `${github.identityToken.decode.payload.actor}`            | Extract GitHub workflow actor                | `octocat`              |
| `${github.identityToken.decode.payload.repository}`       | Extract GitHub repository                    | `owner/repo`           |
| `${github.identityToken.decode.payload.workflow}`         | Extract GitHub workflow name                 | `ci.yml`               |
| `${os.environment.K8S_POD_NAME}`                          | Extract Kubernetes pod name from environment | `my-app-pod-12345`     |
| `${oidc.identityToken.decode.payload.user_login}_dynamic` | Combined value                               | `ci-workload_dynamic`  |

## Configuration examples

### OIDC ID Token Credential Provider

Configure dynamic claims in an [OIDC ID Token Credential Provider](../oidc-id-token.md) as follows:

### Subject field

```plaintext
${oidc.identityToken.decode.payload.user_login}
```

### Custom claims

* **Claim Name**: `workload_email`

* **Value**: `${oidc.identityToken.decode.payload.user_email}_verified`

* **Claim Name**: `dynamic_role`

* **Value**: `${oidc.identityToken.decode.payload.role}`

### JWT-SVID Token Credential Provider

Configure dynamic claims in a [JWT-SVID Token Credential Provider](../spiffe-jwt-svid.md) for SPIFFE-compliant tokens:

#### Subject field (SPIFFE ID)

```plaintext
spiffe://your-domain/ns/${oidc.identityToken.decode.payload.namespace}/sa/${oidc.identityToken.decode.payload.service_account}
```

#### Custom claims

* **Claim Name**: `namespace`

* **Value**: `${oidc.identityToken.decode.payload.namespace}`

* **Claim Name**: `cluster`

* **Value**: `${os.environment.KUBERNETES_PROVIDER_ID}`

### X.509-SVID Credential Provider

Configure dynamic values for the **Subject** and **Spiffe ID** fields in an [X.509-SVID Credential Provider](../spiffe-x509-svid.md) to derive each workload’s identity at issuance time from its Trust Provider attestation.

Unlike OIDC ID Tokens and JWT-SVID Tokens, X.509-SVID certificates do not carry arbitrary custom claims. Dynamic expressions on this Credential Provider therefore apply to the certificate’s identity fields:

* **Spiffe ID** — Resolves to the URI Subject Alternative Name on the issued certificate. This is the primary identity field for SPIFFE-aware Server Workloads.
* **Subject** — Resolves to the X.509 Subject Distinguished Name (DN). SPIFFE conveys identity through the URI SAN, so this field is typically left empty. Populate it when integrating with a Server Workload that isn’t SPIFFE-aware and instead reads the Subject DN for authorization, or when you want descriptive metadata embedded in the certificate. For example, you might set the Subject to `CN=payments-service,OU=billing,O=acme` so the receiving Server Workload can match on the Common Name.

#### SPIFFE ID field examples

For Kubernetes workloads attested by OIDC ID Tokens:

```text
spiffe://example.com/ns/${oidc.identityToken.decode.payload.namespace}/sa/${oidc.identityToken.decode.payload.service_account}
```

For AWS workloads attested by AWS Role:

```text
spiffe://example.com/aws/account/${aws.account}/role/${aws.role}
```

The expression syntax and supported claim sources documented below apply identically to the Subject and Spiffe ID fields on an X.509-SVID Credential Provider.

## Step-by-step example

This example demonstrates extracting GitLab workload information from an OIDC token and using it in generated credentials.

1. **Create an OIDC ID Token Credential Provider** with dynamic claims:

   * **Subject**: `${oidc.identityToken.decode.payload.user_login}test_dynamic`
   * **Custom Claim**: `dynamic_claim1` = `${oidc.identityToken.decode.payload.user_email}_email`

2. **Create supporting Aembit components**:

   * Access Policy linking your workload to the credential provider
   * Client Workload representing your OIDC token source (for example, GitLab CI job)
   * Server Workload representing your target service

3. **Make a credential request** using your OIDC token

4. **Verify the result** - the generated credential contains:

   * **Subject**: `ci-workload_test_dynamic` (if `user_login` was `ci-workload`)
   * **dynamic\_claim1**: `ci.workload@company.com_email` (if `user_email` was `ci.workload@company.com`)

## Supported claim sources

The following sections describe the supported claim sources and how to use them in dynamic claims.

### OIDC token claims

Extract any claim from the incoming OIDC token’s payload:

```text
${oidc.identityToken.decode.payload.<CLAIM_NAME>}
```

**Common GitLab CI OIDC claims**

* `user_login` - GitLab username
* `user_email` - Workload’s email address
* `project_path` - Full project path
* `ref` - Git branch or tag reference
* `job_id` - CI job identifier

**Common GitHub Actions OIDC claims**

* `actor` - GitHub username who triggered the workflow
* `repository` - Repository name in format `owner/repo`
* `ref` - Git reference (branch/tag)
* `workflow` - Workflow filename

**Common Jenkins OIDC claims**

* `sub` - Subject claim (by default, the URL of the Jenkins job)

* `iss` - Jenkins instance issuer URL

* `aud` - Audience claim (configurable)

* \`Build number (included by default)

* `Custom` claims - Jenkins allows administrators to configure additional claims through “Claim templates” using build variables such as:

  * `${JOB_NAME}` - Name of the Jenkins job
  * `${BUILD_NUMBER}` - Build number for the job run
  * `${NODE_NAME}` - Jenkins node where the job ran
  * `${BUILD_USER}` - Username that triggered the build (if available)
  * `${BRANCH_NAME}` - Git branch name (if applicable)
  * Any other Jenkins environment variables

  > **Note**
  >
  > Unlike GitLab and GitHub which have standardized claim names, Jenkins OIDC claims are highly customizable and depend on your configuration.

### Environment variables

You can extract environment variables from Agent Proxy or Aembit CLI process for use in dynamic claims:

```text
${os.environment.<VARIABLE_NAME>}
```

#### Allowlist requirement

By default, Agent Proxy and Aembit CLI capture **no** custom environment variables. To enable capture for dynamic claims, set the [`AEMBIT_ENV_VAR_ALLOWLIST`](../../../../reference/edge-components/edge-component-env-vars.md#aembit_env_var_allowlist) environment variable to a comma-separated list of permitted variable names.

```shell
AEMBIT_ENV_VAR_ALLOWLIST=CORPORATE_APP_ID,WEBSITE_HOSTNAME,AWS_LAMBDA_FUNCTION_NAME
```

For platform-specific guidance on injecting environment variables and the allowlist into Agent Proxy process, see [Configure custom environment variables for Agent Proxy](../../../deploy-install/advanced-options/agent-proxy/configure-custom-env-vars.md).

#### Always-available variables

Dynamic claims can read the following variables regardless of `AEMBIT_ENV_VAR_ALLOWLIST`, provided each one exists in Agent Proxy or Aembit CLI process environment:

* [`K8S_POD_NAME`](../../../../reference/edge-components/edge-component-env-vars.md#k8s_pod_name)
* [`K8S_NAMESPACE`](../../../../reference/edge-components/edge-component-env-vars.md#k8s_namespace)
* [`KUBERNETES_PROVIDER_ID`](../../../../reference/edge-components/edge-component-env-vars.md#kubernetes_provider_id)
* [`AEMBIT_RESOURCE_SET_ID`](../../../../reference/edge-components/edge-component-env-vars.md#aembit_resource_set_id)
* [`CLIENT_WORKLOAD_ID`](../../../../reference/edge-components/edge-component-env-vars.md#client_workload_id)

#### Common examples

* `${os.environment.K8S_POD_NAME}` — Kubernetes pod name
* `${os.environment.CLIENT_WORKLOAD_ID}` — Aembit Client Workload identifier
* `${os.environment.CORPORATE_APP_ID}` — your custom application identifier (must be in the allowlist)

## Behavior and scope

### Process boundary

Aembit reads environment variables only from the **Agent Proxy** or **Aembit CLI** process environment. Variables set only in the Client Workload process aren’t visible to dynamic claims.

### Behavior on missing or non-allowlisted variables

When a Credential Provider references a variable that’s absent from both [`AEMBIT_ENV_VAR_ALLOWLIST`](../../../../reference/edge-components/edge-component-env-vars.md#aembit_env_var_allowlist) and the [always-available variables](#always-available-variables), Agent Proxy logs a warning (`requested env variable <name> is not in allow list`) and omits the variable from the credential request. The request still proceeds, but without that claim value.

### Supported platforms

Aembit captures custom environment variables on Agent Proxy (Linux Virtual Machines, Windows Virtual Machines, and Kubernetes) and Aembit CLI (Linux and Windows Virtual Machines).

## Best practices

The following best practices help you use dynamic claims in both OIDC ID Token and JWT-SVID Token Credential Providers:

### Security considerations

* **Validate input claims** - Ensure the OIDC token contains the expected claims before extraction
* **Limit scope** - Only extract necessary claims to minimize exposure
* **Review generated credentials** - Use tools like [jwt.io](https://jwt.io) to decode and verify generated tokens
* **SPIFFE compliance** - For JWT-SVID tokens, ensure dynamic SPIFFE IDs follow the `spiffe://` format

### Template design

* **Use descriptive names** - Make custom claim names clear and meaningful
* **Combine values with care** - When combining values, ensure the result remains valid for your target service
* **Test the result** - Verify dynamic claims work correctly with your specific OIDC token structure

### Troubleshooting

* **Missing claims** - If a referenced claim doesn’t exist in the source OIDC token, the expression may result in an empty value
* **Token format** - Ensure your OIDC token follows proper formatting and contains the expected payload structure
* **Permissions** - Verify your OIDC provider includes the necessary claims in the token
* **Environment variable not in the allowlist** - If a Credential Provider references an environment variable that isn’t listed in [`AEMBIT_ENV_VAR_ALLOWLIST`](../../../../reference/edge-components/edge-component-env-vars.md#aembit_env_var_allowlist) (and isn’t one of the [always-available variables](#always-available-variables)), Agent Proxy logs a warning to the effect of `requested env variable <name> is not in allow list` and omits the variable from the credential request. Add the variable name to the allowlist and restart Agent Proxy or Aembit CLI process so the claim resolves.
* **Environment variable missing from the process** - Agent Proxy or Aembit CLI only sees variables in its own process environment. See [Configure custom environment variables for Agent Proxy](../../../deploy-install/advanced-options/agent-proxy/configure-custom-env-vars.md) for platform-specific injection guidance.

## Related docs

* [Configure custom environment variables for Agent Proxy](../../../deploy-install/advanced-options/agent-proxy/configure-custom-env-vars.md): how to inject custom variables and set the allowlist on each platform
* [Edge Component environment variables reference](../../../../reference/edge-components/edge-component-env-vars.md)
* [Create an OIDC ID Token Credential Provider](../oidc-id-token.md)
* [About the OIDC ID Token Credential Provider](../about-oidc-id-token.md)
* [Create a JWT-SVID Token Credential Provider](../spiffe-jwt-svid.md)
* [About the JWT-SVID Token Credential Provider](../about-spiffe-jwt-svid.md)
* [Vault Dynamic Claims](dynamic-claims-vault.md) (for Vault-specific dynamic claims)
