---
type: how-to
title: "Credential name support"
description: "How to name credentials with the Aembit CLI --credential-names option, and how to use a different name for a credential"
resource: https://docs.aembit.io/dev-guide/cli/credential-names/
interface: cli
tags: ["cli"]
timestamp: 2026-09-09T09:33:20-07:00
---

# Credential name support

The `--credential-names` option of [`aembit credentials get`](reference/credentials-get.md) tells your Aembit Tenant which names to return credentials under. Aembit CLI writes one environment variable statement per credential to standard output, in the format that [`--output-format`](reference/credentials-get.md#--output-format) selects.

## Name a credential

Pass the name your application already reads, and Aembit CLI returns the credential under that name:

```shell
eval $(aembit credentials get \
  --client-id aembit:useast2:a12bc3:identity:github_idtoken:63ab7be6-9785-4a14-be1c-2acf0253070b \
  --server-workload-host example.com \
  --server-workload-port 443 \
  --credential-names API_TOKEN)
```

Most Credential Providers hold a single credential and return it under whatever name you pass. Pass one name for those Credential Providers, because passing two names returns the same credential twice, once under each name. If you omit `--credential-names`, Aembit CLI requests the default name `TOKEN`.

## Credential Providers that limit the names you can use

Your Aembit Tenant resolves each name you pass against the Credential Provider that the matched Access Policy maps. Some Credential Providers recognize a fixed set of names. Requesting a name that the Credential Provider doesn’t recognize returns an error naming the credential your Aembit Tenant couldn’t resolve:

```text
Credential(s) not returned by tenant: MY_TOKEN
```

Aembit CLI writes the credentials that your Aembit Tenant did resolve to standard output before it reports this error. A call that passes more than one name can therefore succeed for some names and fail for the rest.

The Model Context Protocol (MCP) Credential Provider [MCP User-Based Access Token](../../user-guide/access-policies/credential-providers/mcp-user-based-access-token.md) identifies an end user rather than a workload, so Aembit CLI can’t retrieve its credentials.

### Credential Providers that recognize fixed names

The following Credential Providers recognize their own names, and you pass those names instead of a name you choose:

* [AWS Secrets Manager Value](../../user-guide/access-policies/credential-providers/aws-secrets-manager.md) recognizes `CREDENTIAL`, `USERNAME`, and `PASSWORD`, narrowed by how you configured the secret
* [Azure Key Vault Value](../../user-guide/access-policies/credential-providers/azure-key-vault.md) recognizes the same three names, and also accepts any name you choose when you configure it as Single Key
* [JSON Web Token (JWT)](../../user-guide/access-policies/credential-providers/json-web-token.md) returns its signed token under `TOKEN` or `CREDENTIAL`, and the subject claim under `USERNAME`
* [Username & Password](../../user-guide/access-policies/credential-providers/username-password.md) recognizes `USERNAME` and `PASSWORD`, and returns one Base64-encoded `username:password` value under any name you pass when the Server Workload uses HTTP Basic authentication
* [X.509-SVID Certificate](../../user-guide/access-policies/credential-providers/spiffe-x509-svid.md) releases its value only under `CLIENT_CERT_CHAIN`

Pass these names exactly, because your Aembit Tenant matches them case-sensitively. AWS Secrets Manager Value and Azure Key Vault Value report a name that doesn’t match their configuration differently:

```text
Requested credential is not available for the current configuration of this Credential Provider.
```

### Credential Providers that return multiple values

[AWS Security Token Service Federation](../../user-guide/access-policies/credential-providers/aws-security-token-service-federation.md) and [Vault Client Token](../../user-guide/access-policies/credential-providers/vault-client-token.md) accept any name you pass. A name of your choosing collapses their values into one JSON document returned under that name. To receive each value under its own name, pass all the provider’s names in the same call:

```shell
eval $(aembit credentials get \
  --client-id aembit:useast2:a12bc3:identity:github_idtoken:63ab7be6-9785-4a14-be1c-2acf0253070b \
  --server-workload-host example.com \
  --server-workload-port 443 \
  --credential-names AWS_ACCESS_KEY_ID,AWS_SECRET_ACCESS_KEY,AWS_SESSION_TOKEN)
```

AWS Security Token Service Federation splits its values out only when all three names appear together, so omitting one returns the JSON document instead. Vault Client Token takes `TOKEN` and `INDEX`, and requesting `INDEX` without `TOKEN` returns `Credential(s) not returned by tenant: INDEX`.

## Use a different name for a credential

When your application reads a name that the Credential Provider doesn’t recognize, request the name the Credential Provider recognizes and copy the value into the variable your application reads.

On Linux and macOS:

```shell
eval $(aembit credentials get \
  --client-id aembit:useast2:a12bc3:identity:github_idtoken:63ab7be6-9785-4a14-be1c-2acf0253070b \
  --server-workload-host example.com \
  --server-workload-port 443 \
  --credential-names TOKEN)


export MY_APP_TOKEN="$TOKEN"
```

On Windows:

```powershell
$result = (& ".\aembit.exe" credentials get `
  --client-id aembit:useast2:a12bc3:identity:github_idtoken:63ab7be6-9785-4a14-be1c-2acf0253070b `
  --server-workload-host example.com `
  --server-workload-port 443 `
  --credential-names TOKEN)


Invoke-Expression $result
$env:MY_APP_TOKEN = $env:TOKEN
```

## When Aembit CLI returns a name you didn’t request

An Access Policy can deliver a credential that Aembit Edge acquired and named on your behalf. In that case, Aembit CLI returns the credential under its configured name and doesn’t send your requested names to your Aembit Tenant. Check the Server Workload’s configuration in your Aembit Tenant for the name to expect. Read the names from the Aembit CLI output rather than assuming the names you requested.

## Related topics

* [`aembit credentials get`](reference/credentials-get.md)
* [Getting credentials](usage/get-credentials.md)
* [Getting credentials on Windows](usage/get-credentials-windows.md)
* [Troubleshooting Aembit CLI](troubleshooting.md)
