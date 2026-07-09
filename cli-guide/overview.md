---
type: explanation
title: "Aembit CLI"
description: "Overview Aembit's CLI"
resource: https://docs.aembit.io/cli-guide/
timestamp: 2026-04-07T10:38:34-07:00
type_inferred: true
---

# Aembit CLI

Aembit CLI is a command-line interface tool that enables you to get credentials to access a Server Workload directly from your terminal.

![Rocket Icon](https://docs.aembit.io/aembit-icons/rocket.svg)

[Use the Aembit CLI ](usage/overview.md)Set-up and usage guides for Aembit CLI.

→

![Code Icon](https://docs.aembit.io/aembit-icons/code-solid.svg)

[Command Reference ](reference/overview.md)View all Aembit CLI commands and their options.

→

## Supported operating systems

[Section titled “Supported operating systems”](#supported-operating-systems)

Aembit CLI is available for the following operating systems:

* **Linux** - Aembit CLI is available as a binary package for Linux.
* **Windows Server 2019 and 2022** - Aembit CLI is available as a binary package for Windows.
* **Windows IoT Enterprise 2021 LTSC** - Aembit CLI is available as a binary package for Windows IoT Enterprise.

CLI on Windows

When using Aembit CLI on Windows, you must use Windows PowerShell. The [`--output-format`](reference/credentials-get.md#--output-format) of Aembit CLI commands isn’t compatible with the Windows Command Prompt (`cmd.exe`).

Aembit also recommends avoiding PowerShell Integrated Scripting Environment (ISE), as it may not handle certain commands correctly. Use the standard PowerShell terminal instead.

## Supported Trust Providers

[Section titled “Supported Trust Providers”](#supported-trust-providers)

Aembit CLI supports certain [Trust Providers](../get-started/concepts/trust-providers.md) to retrieve credentials for Client Workloads through the command line. Aembit uses these Trust Providers to verify the identity of any requesting Client Workloads and ensure that Aembit retrieves the correct credentials for that workload.

Aembit CLI supports the following Trust Provider identity types:

* [AWS Role](../user-guide/access-policies/trust-providers/aws-role-trust-provider.md) and [AWS Metadata Service](../user-guide/access-policies/trust-providers/aws-metadata-service-trust-provider.md)
* [GitHub](../user-guide/access-policies/trust-providers/github-trust-provider.md)
* [GitLab](../user-guide/access-policies/trust-providers/gitlab-trust-provider.md)
* [Kubernetes Service Account](../user-guide/access-policies/trust-providers/kubernetes-service-account-trust-provider.md)
* [Generic OIDC ID Token](../user-guide/access-policies/trust-providers/oidc-id-token-trust-provider.md) which supports other CI/CD platforms, such as Jenkins, that can provide OIDC-compliant ID tokens.

If you don’t have a Trust Provider set up, you can follow the steps in the [Aembit User Guide](../user-guide/access-policies/trust-providers/overview.md) to create one.

## Supported Credential Providers

[Section titled “Supported Credential Providers”](#supported-credential-providers)

The type of credentials output by Aembit CLI depends on the [Credential Provider](../user-guide/access-policies/credential-providers/overview.md) configured on the Access Policy in your Aembit Tenant. Not all Credential Providers output the same type of credentials, and some require that you use specific credential names when retrieving credentials.

**Credential Providers that don’t expect a specific credential names**:

* [Aembit Access Token](../user-guide/access-policies/credential-providers/aembit-access-token.md)
* [API Key](../user-guide/access-policies/credential-providers/api-key.md)
* [JSON Web Token (JWT)](../user-guide/access-policies/credential-providers/json-web-token.md)
* [OAuth 2.0 Authorization Code](../user-guide/access-policies/credential-providers/oauth-authorization-code.md)
* [OAuth 2.0 Client Credentials](../user-guide/access-policies/credential-providers/oauth-client-credentials.md)
* [OIDC ID Token](../user-guide/access-policies/credential-providers/oidc-id-token.md)

The preceding Credential Providers output a single credential, which you can use directly in your scripts or applications. You can use the `--credential-names` option to rename the output credential to a name of your choice.

**Credential Providers that expect specific credential names**:

* [Username & Password](../user-guide/access-policies/credential-providers/username-password.md)\
  This Credential Provider outputs two credentials: `USERNAME` and `PASSWORD`. You can use the `--credential-names` option to specify the names of these credentials when retrieving them.
