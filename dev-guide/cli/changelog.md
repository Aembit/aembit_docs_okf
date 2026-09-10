---
type: reference
title: "Aembit CLI changelog"
description: "Aembit CLI changelog"
resource: https://docs.aembit.io/dev-guide/cli/changelog/
interface: cli
tags: ["cli"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Aembit CLI changelog

## Version history

| Aembit CLI Version   | Release Date | Platforms                            | Notes                                                                                                                                                                                                                                                                       |
| -------------------- | ------------ | ------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.34.5772            | 8/27/2026    | Linux (amd64, arm64) Windows (amd64) | Add a glibc-linked Linux amd64 archive alongside the default musl build                                                                                                                                                                                                     |
| 1.32.4999            | 6/4/2026     | Linux (amd64, arm64) Windows (amd64) | Add AWS (instance metadata or IAM role) and Kubernetes Trust Provider authentication to credentials get alongside OIDC tokens; add X.509 certificate retrieval for the X.509-SVID Credential Provider through a new client private-key option; add HTTP/2 keepalive options |
| 1.31.4764            | 5/2/2026     | Linux (amd64, arm64) Windows (amd64) | Add upstream HTTP proxy support; add --client-workload-id flag and OIDC token expiration validation; add support for gathering dynamic claims from environment variables                                                                                                    |
| 1.31.4670            | 4/17/2026    | Linux (amd64, arm64) Windows (amd64) | Add upstream HTTP proxy support for Aembit Cloud and Server Workload traffic                                                                                                                                                                                                |
| 1.30.4433            | 3/11/2026    | Linux (amd64, arm64) Windows (amd64) | No CLI code changes; build pipeline and artifact signing updates only                                                                                                                                                                                                       |
| 1.29.4298            | 2/26/2026    | Linux (amd64, arm64) Windows (amd64) | Internal changes to process identification and assessment caching shared with Agent Proxy; no CLI-visible change                                                                                                                                                            |
| 1.28.4063            | 1/16/2026    | Linux (amd64, arm64) Windows (amd64) | Internal changes shared with Agent Proxy; no CLI-visible change                                                                                                                                                                                                             |
| 1.27.3865            | 12/4/2025    | Linux (amd64, arm64) Windows (amd64) | Extend attestation gathering with VMware network attestation and Client Workload binary hashing; dependency updates                                                                                                                                                         |
| 1.26.3639            | 10/21/2025   | Linux (amd64, arm64) Windows (amd64) | Toolchain and dependency cleanup                                                                                                                                                                                                                                            |
| 1.25.3600            | 10/2/2025    | Linux (amd64, arm64) Windows (amd64) | No CLI code changes; rebuilt with updated base image and dependencies                                                                                                                                                                                                       |
| 1.25.3494            | 8/22/2025    | Linux (amd64, arm64) Windows (amd64) | Print help when run with no subcommand; add the sh environment-variable output format; clearer error messages when an OIDC token is missing or credential retrieval fails                                                                                                   |
| 1.24.3328            | 7/29/2025    | Linux (amd64, arm64) Windows (amd64) |                                                                                                                                                                                                                                                                             |
| 1.17.0               | 9/18/2024    | Linux (amd64, arm64)                 |                                                                                                                                                                                                                                                                             |
| 1.13.0               | 3/27/2024    | Linux (amd64, arm64)                 |                                                                                                                                                                                                                                                                             |
| Show 9 more versions |              |                                      |                                                                                                                                                                                                                                                                             |

The version number has three parts: `major.minor.patch`. For example, `1.24.3328` indicates:

* **Major version**: `1` - This indicates a major release that may include breaking changes.
* **Minor version**: `24` - This indicates a minor release that adds new features or improvements without breaking existing functionality.
* **Patch version**: `3328` - This indicates a patch release that includes bug fixes or minor improvements.

## Changelog

### Unreleased

#### Aembit CLI (unreleased)

* Added support for the AWS Metadata Service, AWS Role, and Kubernetes Service Account Trust Providers to `credentials get`. Aembit CLI gathers attestation data from the local environment (Instance Metadata Service (IMDS), STS `GetCallerIdentity`, or the projected service account token), so `--id-token` isn’t needed for these Trust Providers.
* Added `vm`, `kubernetes`, `ecs_fargate`, and `lambda_container` as accepted values for the [`--deployment-model`](reference/credentials-get.md#--deployment-model) option. The AWS Role Trust Provider requires this option.

### August 27, 2026

#### Aembit CLI 1.34.5772

* Added a second Linux amd64 archive, `aembit_agent_cli_linux_amd64_glibc_<version>.tar.gz`, that links dynamically against glibc 2.28 or newer. The default archive still links statically against musl and remains the recommended download. Use the glibc build when the default build reports `failed to lookup address information` on a host where `dig` and `curl` resolve the same name. See [Choose a Linux build](usage/setup.md#choose-a-linux-build).

### June 3, 2026

#### Aembit CLI 1.32.4999

* Added `--client-tls-private-key` option (and the `AEMBIT_CLIENT_TLS_PRIVATE_KEY` environment variable) to the `credentials get` command for retrieving X.509-SVID certificates. Aembit CLI generates a Certificate Signing Request (CSR) locally from the supplied private key, submits it through the credential retrieval flow, and returns the signed certificate chain in `CLIENT_CERT_CHAIN`. See [`aembit credentials get --client-tls-private-key`](reference/credentials-get.md#--client-tls-private-key).

### May 1, 2026

#### Aembit CLI 1.31.1

* Added `--client-workload-id` option to the `credentials get` command. Use this to identify a specific Client Workload when multiple workloads share the same Trust Provider. Supply the workload’s Aembit Client ID, not its own resource ID.
* Added expiration validation for OIDC tokens provided with `--id-token`.

### July 22, 2025

Initial release!
