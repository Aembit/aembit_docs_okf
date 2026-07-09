---
type: reference
title: "Aembit CLI changelog"
description: "Aembit CLI changelog"
resource: https://docs.aembit.io/cli-guide/changelog/
interface: cli
timestamp: 2026-06-11T11:10:32-04:00
type_inferred: true
---

# Aembit CLI changelog

## Version history

[Section titled “Version history”](#version-history)

| Aembit CLI Version | Release Date | Platforms                            | Notes                                                                                                                                                                    |
| ------------------ | ------------ | ------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 1.31.4764          | 5/2/2026     | Linux (amd64, arm64) Windows (amd64) | Add upstream HTTP proxy support; add --client-workload-id flag and OIDC token expiration validation; add support for gathering dynamic claims from environment variables |
| 1.24.3328          | 7/29/2025    | Linux (amd64, arm64) Windows (amd64) |                                                                                                                                                                          |

The version number has three parts: `major.minor.patch`. For example, `1.24.3328` indicates:

* **Major version**: `1` - This indicates a major release that may include breaking changes.
* **Minor version**: `24` - This indicates a minor release that adds new features or improvements without breaking existing functionality.
* **Patch version**: `3328` - This indicates a patch release that includes bug fixes or minor improvements.

## Changelog

[Section titled “Changelog”](#changelog)

### TBA, 2026

[Section titled “TBA, 2026”](#tba-2026)

#### Aembit CLI (version TBA)

[Section titled “Aembit CLI (version TBA)”](#aembit-cli-version-tba)

* Added support for the AWS Metadata Service, AWS Role, and Kubernetes Service Account Trust Providers to `credentials get`. Aembit CLI gathers attestation data from the local environment (IMDS, STS `GetCallerIdentity`, or the projected service account token), so `--id-token` isn’t needed for these Trust Providers.
* Added `vm`, `kubernetes`, `ecs_fargate`, and `lambda_container` as accepted values for the [`--deployment-model`](reference/credentials-get.md#--deployment-model) option. This option is required for the AWS Role Trust Provider.

### June 3, 2026

[Section titled “June 3, 2026”](#june-3-2026)

#### Aembit CLI 1.32.TBA

[Section titled “Aembit CLI 1.32.TBA”](#aembit-cli-132tba)

* Added `--client-tls-private-key` option (and the `AEMBIT_CLIENT_TLS_PRIVATE_KEY` environment variable) to the `credentials get` command for retrieving X.509-SVID certificates. Aembit CLI generates a CSR locally from the supplied private key, submits it through the credential retrieval flow, and returns the signed certificate chain in `CLIENT_CERT_CHAIN`. See [`aembit credentials get --client-tls-private-key`](reference/credentials-get.md#--client-tls-private-key).

### May 1, 2026

[Section titled “May 1, 2026”](#may-1-2026)

#### Aembit CLI 1.31.1

[Section titled “Aembit CLI 1.31.1”](#aembit-cli-1311)

* Added `--client-workload-id` option to the `credentials get` command. Use this to specify a Client Workload ID when multiple workloads share the same Trust Provider.
* Added expiration validation for OIDC tokens provided with `--id-token`.

### July 22, 2025

[Section titled “July 22, 2025”](#july-22-2025)

Initial release!
