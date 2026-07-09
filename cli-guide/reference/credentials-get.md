---
type: reference
title: "aembit credentials get"
description: "A guide to managing credentials with Aembit CLI"
resource: https://docs.aembit.io/cli-guide/reference/credentials-get/
interface: cli
tags: [reference]
timestamp: 2026-06-18T13:15:52-04:00
type_inferred: true
---

# aembit credentials get

Aembit CLI provides the `credentials get` command to retrieve credentials for a specific Client Workload. This command is useful for obtaining credentials that you can use in your scripts or applications to access resources protected by Aembit Access Policies.

**General usage**:

```shell
aembit credentials get [OPTIONS] \
  --client-id <CLIENT_ID> \
  --server-workload-host <HOST> \
  --server-workload-port <PORT>
```

**Get help**:

```shell
aembit credentials get -h | --help
```

This command requires the following options:

* `--client-id`
* `--server-workload-host`
* `--server-workload-port`

Where the `--client-id` represents the Edge SDK Client ID from your Aembit Trust Provider in your Aembit Tenant that Agent CLI uses to identify itself. To retrieve Edge SDK Client ID, see [Find your Edge SDK Client ID](../../user-guide/access-policies/trust-providers/get-edge-sdk-client-id.md).

It’s formatted as follows:

```plaintext
aembit:<region>:<tenantId>:identity:<identityType>:<trustProviderId>
```

Detailed example

If your Aembit Tenant is deployed in the `useast2` region, has a tenant ID of `a12bc3`, and uses a GitLab identity type with a trust provider ID of `63ab7be6-9785-4a14-be1c-2acf0253070b`, your client ID would look like this:

```shell
aembit:useast2:a12bc3:identity:gitlab_idtoken:63ab7be6-9785-4a14-be1c-2acf0253070b
```

* `useast2` is the `<region>` where the Aembit stack is deployed
* `a12bc3` is the `<tenantId>` of your Aembit Tenant
* `gitlab_idtoken` is the `<identityType>`
* `63ab7be6-9785-4a14-be1c-2acf0253070b` is the `<trustProviderId>`

`eval` usage

When you run the `aembit credentials get` command, it outputs the credentials in a format that you can use directly in your shell environment. To use these credentials, you can pipe the output to the `eval` command, which executes the output as shell commands.

You can also specify the `--credential-names` option to assign specific names to the credentials that Aembit retrieves. This is useful for when you want to use specific names for the credentials in your scripts or applications. When you’re using a Credential Provider that requires specific credential names. To set multiple credentials, put them in a comma-separated list.

If you don’t specify this option, Aembit uses the default name `TOKEN`.

For example, you can use the following command to retrieve and export the credentials for the Username and Password Credential Provider:

```shell
eval $(aembit credentials get \
  --client-id aembit:useast2:a12bc3:identity:github_idtoken:63ab7be6-9785-4a14-be1c-2acf0253070b \
  --server-workload-host example.com \
  --server-workload-port 443 \
  --id-token <ID_TOKEN> \
  --credential-names USERNAME,PASSWORD)
```

## Options

[Section titled “Options”](#options)

### `--client-id` Required

[Section titled “--client-id ”](#--client-id)

**Default** - not set\
**Agent Proxy env var**: [AEMBIT\_CLIENT\_ID](../../reference/edge-components/edge-component-env-vars.md#aembit_client_id)\
**Description** - This value represents the Edge SDK Client ID from your Aembit Trust Provider. Aembit automatically generates the Edge SDK Client ID when you configure a Trust Provider in your Aembit Tenant UI. To retrieve your Edge SDK Client ID, see [Find your Edge SDK Client ID](../../user-guide/access-policies/trust-providers/get-edge-sdk-client-id.md).\
**Example** - `aembit:useast2:a12bc3:identity:github_idtoken:63ab7be6-9785-4a14-be1c-2acf0253070b`

### `--server-workload-host` Required

[Section titled “--server-workload-host ”](#--server-workload-host)

**Default** - not set\
**Description** - The server hostname or IP address, Aembit uses to match an Access Policy\
**Examples** - `example.com`, `localhost`, or an IP address

### `--server-workload-port` Required

[Section titled “--server-workload-port ”](#--server-workload-port)

**Default** - not set\
**Description** -The server port number, Aembit uses to match an Access Policy\
**Examples** - `443`, `8443`, `8080`, etc.

### `--id-token`

[Section titled “--id-token”](#--id-token)

**Default** - not set\
**Description** - The OIDC token from the platform associated with the Trust Provider that Aembit uses for attestation. Use this flag with OIDC-token Trust Providers: GitHub, GitLab, and the generic OIDC ID Token Trust Provider. The AWS Role, AWS Metadata Service, and Kubernetes Service Account Trust Providers don’t use `--id-token`; Aembit CLI gathers attestation data from the local environment instead. See [`--deployment-model`](#--deployment-model) for the values Aembit CLI uses to select that environment.

GitLab Trust Provider

If you are using a GitLab Trust Provider, you must provide the `--id-token` option with a valid OIDC token.

### `--client-tls-private-key`

[Section titled “--client-tls-private-key”](#--client-tls-private-key)

**Default** - not set\
**Env var**: [AEMBIT\_CLIENT\_TLS\_PRIVATE\_KEY](../../reference/edge-components/edge-component-env-vars.md#aembit_client_tls_private_key)\
**Description** - The path to a PEM-encoded private key file used to retrieve an X.509-SVID certificate from an X.509-SVID Credential Provider. Aembit CLI generates a Certificate Signing Request (CSR) from the supplied key, submits it through the existing credential retrieval flow, and returns the signed certificate chain in `CLIENT_CERT_CHAIN`. The private key never leaves the local machine.\
**Example** - `/etc/aembit/client.key`

When you use this option, an X.509-SVID Credential Provider must be configured in your Aembit Tenant and attached to the matching Access Policy. See [About the X.509-SVID Credential Provider](../../user-guide/access-policies/credential-providers/about-spiffe-x509-svid.md) for concepts and [Create an X.509-SVID Credential Provider](../../user-guide/access-policies/credential-providers/spiffe-x509-svid.md) for configuration steps.

**Key file requirements**:

* The file must contain a single **PEM-encoded Public-Key Cryptography Standards #8 (PKCS #8)** private key. Other PEM encodings (PKCS #1 RSA, SEC1 EC) aren’t accepted.
* The key must be **unencrypted** at rest. Passphrase-protected keys aren’t supported.
* The path must resolve to a regular file the CLI process can read.

To confirm a key is PKCS #8, check the first line:

```shell
head -1 client.key
# Expected: -----BEGIN PRIVATE KEY-----
```

If your key is in PKCS #1 or SEC1 form (`-----BEGIN RSA PRIVATE KEY-----` or `-----BEGIN EC PRIVATE KEY-----`), convert it to PKCS #8 with `openssl`:

```shell
openssl pkcs8 -topk8 -nocrypt -in legacy.key -out client.key
```

Aembit Cloud signs CSRs that use any of the following key algorithms: RSA (2048 or 4096 bits) or ECDSA (P-256 or P-384). The CLI rejects keys outside these ranges. Choose an algorithm and key size that meet your requirements within those bounds.

### `--client-workload-id`

[Section titled “--client-workload-id”](#--client-workload-id)

**Default** - not set\
**Agent Proxy env var**: [CLIENT\_WORKLOAD\_ID](../../reference/edge-components/edge-component-env-vars.md#client_workload_id)\
**Description** - The Client Workload ID that Aembit uses to identify a specific Client Workload when multiple workloads share the same Trust Provider. Use this when your environment has two or more Client Workloads that would otherwise match the same Trust Provider attestation.\
**Example** - `7e75e718-7634-480b-9f7b-a07bb5a4f11d`

`--client-workload-id` vs `--client-id`

These two flags serve different purposes:

* **`--client-id`** — identifies your Aembit Tenant and Trust Provider. It’s the Edge SDK Client ID that encodes your region, tenant, and Trust Provider information.
* **`--client-workload-id`** — identifies a specific Client Workload within your tenant. It’s a UUID assigned to the Client Workload in your Aembit Tenant UI.

### `--credential-names`

[Section titled “--credential-names”](#--credential-names)

**Default** - `TOKEN`\
**Description** - The names to give the credentials that Aembit receives from the Credential Provider. This is useful for when you want to use specific names for the credentials in your scripts or applications. You can specify multiple names by separating them with commas.\
**Examples** - `MY_TOKEN,MY_SECRET`, `MY_ACCESS_TOKEN,MY_REFRESH_TOKEN`

X.509-SVID retrieval requires `CLIENT_CERT_CHAIN`

When you retrieve an X.509-SVID certificate using `--client-tls-private-key`, you must include `CLIENT_CERT_CHAIN` in `--credential-names`. Aembit CLI always returns the signed certificate chain under that exact name.

If you supply `--credential-names` without `CLIENT_CERT_CHAIN`, the CLI returns:

```plaintext
Credential(s) not returned by tenant: <YOUR_CUSTOM_NAME>
```

You can request `CLIENT_CERT_CHAIN` alongside other credential names in the same call.

### `--deployment-model`

[Section titled “--deployment-model”](#--deployment-model)

**Default** - not set\
**Possible values** - `vm`, `kubernetes`, `ecs_fargate`, `lambda_container`\
**Description** - Tells Aembit CLI which environment-specific data to gather when it attests to Aembit Cloud. Set this flag to match where the CLI runs so Aembit CLI can locate the IMDS endpoint, projected service account token, or container metadata it needs.

| Trust Provider             | Where you run Aembit CLI          | `--deployment-model` value | Required?    |
| -------------------------- | --------------------------------- | -------------------------- | ------------ |
| AWS Metadata Service       | EC2 instance                      | `vm`                       | Recommended  |
| AWS Role                   | EC2 instance                      | `vm`                       | **Required** |
| AWS Role                   | Containerized AWS Lambda function | `lambda_container`         | **Required** |
| Kubernetes Service Account | Any pod                           | `kubernetes`               | Recommended  |

AWS Role attestation requires `--deployment-model`

Aembit CLI only assembles the AWS Security Token Service (STS) `GetCallerIdentity` attestation when `--deployment-model` is `vm`, `ecs_fargate`, or `lambda_container`. If you omit `--deployment-model` with an AWS Role Trust Provider, the attestation is empty and Access Policy matching fails.

### `--log-level`

[Section titled “--log-level”](#--log-level)

**Default** - `warn`\
**Possible values** - `off`, `trace`, `debug`, `info`, `warn`, `error`\
**Agent Proxy env var**: [AEMBIT\_LOG\_LEVEL](../../reference/edge-components/edge-component-env-vars.md#aembit_log_level)\
**Description** - The log level to use for the Aembit CLI. This controls the verbosity of the output from the CLI.

### `--output-format`

[Section titled “--output-format”](#--output-format)

**Default** - `sh-export`\
**Possible values** - `sh-export`, `sh-env`, `powershell-env`

**Description** - This option determines how Aembit CLI formats the credentials in the output.\
You can choose from the following formats:

* `sh-export` - credentials returned as exported POSIX-compatible environment variables.\
  *Example*: `export KEY=val`
* `sh-env` - credentials returned as raw, POSIX-compatible environment variables.\
  *Example*: `KEY=val`
* `powershell-env` - credentials returned as Windows PowerShell-compatible environment variables for consumption by PowerShell Invoke-Expression.\
  *Example*: `$env:KEY = "val"`

### `--resource-set-id`

[Section titled “--resource-set-id”](#--resource-set-id)

**Default** - not set\
**Agent Proxy env var**: [AEMBIT\_RESOURCE\_SET\_ID](../../reference/edge-components/edge-component-env-vars.md#aembit_resource_set_id)\
**Description** - The [Resource Set](../../user-guide/administration/resource-sets/overview.md) to authenticate against and within which the Access Policy matching happens.\
This is useful for when you want to use a specific Resource Set for your credentials. You can find the Resource Set ID in your Aembit Tenant UI under the Resource Sets section.

## Examples

[Section titled “Examples”](#examples)

Each of the following examples demonstrates how to use the `aembit credentials get` command with different options.

All commands include the following required options:

* `--client-id`
* `--server-workload-host`
* `--server-workload-port`

`eval` usage

Use the `eval` command to execute the output as shell commands, allowing you to use the credentials directly in your script or application.

```shell
# Get credentials for a specific client workload
aembit credentials get \
  --client-id aembit:useast2:a12bc3:identity:github_idtoken:63ab7be6-9785-4a14-be1c-2acf0253070b \
  --server-workload-host example.com \
  --server-workload-port 443 \
  --id-token <ID_TOKEN>
```



```shell
# Get credentials with all options
aembit credentials get \
  --client-id aembit:useast2:a12bc3:identity:github_idtoken:63ab7be6-9785-4a14-be1c-2acf0253070b \
  --server-workload-host example.com \
  --server-workload-port 443 \
  --id-token <ID_TOKEN> \
  --credential-names MY_TOKEN,MY_SECRET \
  --output-format powershell-env \
  --deployment-model vm \
  --resource-set-id my-resource-set-id
```



```shell
# Get credentials with custom names
aembit credentials get \
  --client-id aembit:useast2:a12bc3:identity:github_idtoken:63ab7be6-9785-4a14-be1c-2acf0253070b \
  --server-workload-host example.com \
  --server-workload-port 443 \
  --credential-names MY_TOKEN,MY_SECRET
```



```shell
# Get credentials with output format
aembit credentials get \
  --client-id aembit:useast2:a12bc3:identity:github_idtoken:63ab7be6-9785-4a14-be1c-2acf0253070b \
  --server-workload-host example.com \
  --server-workload-port 443 \
  --output-format powershell-env
```



```shell
# Get credentials with deployment model
aembit credentials get \
  --client-id aembit:useast2:a12bc3:identity:github_idtoken:63ab7be6-9785-4a14-be1c-2acf0253070b \
  --server-workload-host example.com \
  --server-workload-port 443 \
  --id-token <ID_TOKEN> \
  --deployment-model vm
```



```shell
# Get credentials with resource set ID
aembit credentials get \
  --client-id aembit:useast2:a12bc3:identity:github_idtoken:63ab7be6-9785-4a14-be1c-2acf0253070b \
  --server-workload-host example.com \
  --server-workload-port 443 \
  --id-token <ID_TOKEN> \
  --resource-set-id 78bg7be6-9301-hj14-d51c-2acf02530y67
```



```shell
# Get credentials with log level
aembit credentials get \
  --client-id aembit:useast2:a12bc3:identity:github_idtoken:63ab7be6-9785-4a14-be1c-2acf0253070b \
  --server-workload-host example.com \
  --server-workload-port 443 \
  --log-level debug
```



```shell
# Get credentials with a client workload ID
aembit credentials get \
  --client-id aembit:useast2:a12bc3:identity:github_idtoken:63ab7be6-9785-4a14-be1c-2acf0253070b \
  --server-workload-host example.com \
  --server-workload-port 443 \
  --id-token eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJyZXBvOmExMmJjMy9teWFwcDpyZWY6cmVmcy9oZWFkcy9tYWluIiwiaXNzIjoiaHR0cHM6Ly90b2tlbi5hY3Rpb25zLmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImF1ZCI6Imh0dHBzOi8vYTEyYmMzLmFlbWJpdC5pbyJ9.UZQP3rK8mN2xL9vY4tH7wF1bSjEeAoCdIgThPkXlVmWy \
  --client-workload-id 7e75e718-7634-480b-9f7b-a07bb5a4f11d
```



```shell
# Get an X.509-SVID certificate
aembit credentials get \
  --client-id aembit:useast2:a12bc3:identity:github_idtoken:63ab7be6-9785-4a14-be1c-2acf0253070b \
  --server-workload-host example.com \
  --server-workload-port 443 \
  --id-token <ID_TOKEN> \
  --client-tls-private-key /etc/aembit/client.key \
  --credential-names CLIENT_CERT_CHAIN
```



```shell
# Get credentials with the AWS Metadata Service Trust Provider on an EC2 instance
# Aembit CLI reads the instance identity document from IMDS, so no token is required
eval $(aembit credentials get \
  --client-id aembit:useast2:a12bc3:identity:aws_metadata:b1234567-abcd-1234-efab-123456789012 \
  --server-workload-host example.com \
  --server-workload-port 443 \
  --deployment-model vm)
```



```shell
# Get credentials with the AWS Role Trust Provider on an EC2 instance
# Aembit CLI builds an STS GetCallerIdentity request from the instance role; --deployment-model vm is required
eval $(aembit credentials get \
  --client-id aembit:useast2:a12bc3:identity:aws_role:c2345678-bcde-2345-fabc-234567890123 \
  --server-workload-host example.com \
  --server-workload-port 443 \
  --deployment-model vm)
```



```shell
# Get credentials with the AWS Role Trust Provider in a containerized AWS Lambda function
# Aembit CLI uses the function's execution role via STS; --deployment-model lambda_container is required
eval $(aembit credentials get \
  --client-id aembit:useast2:a12bc3:identity:aws_role:c2345678-bcde-2345-fabc-234567890123 \
  --server-workload-host example.com \
  --server-workload-port 443 \
  --deployment-model lambda_container)
```



```shell
# Get credentials with the Kubernetes Service Account Trust Provider from a pod
# Aembit CLI reads the projected service account token at /var/run/secrets/kubernetes.io/serviceaccount/token
eval $(aembit credentials get \
  --client-id aembit:useast2:a12bc3:identity:kubernetes_serviceaccount_token:d3456789-cdef-3456-fabc-345678901234 \
  --server-workload-host example.com \
  --server-workload-port 443 \
  --deployment-model kubernetes)
```
