---
type: reference
title: "Edge Component Supported Versions"
description: "Supported versions and release dates for Aembit Edge Components and packages"
resource: https://docs.aembit.io/reference/edge-components/edge-component-supported-versions/
tags: ["edge-component"]
timestamp: 2026-09-09T15:44:57-07:00
---

# Edge Component Supported Versions

Aembit Edge Components and packages are frequently updated with feature enhancements, bug fixes, and additional functionality.

The compatibility matrices shown on this page list the supported versions for:

[**Aembit Edge Components**](#supported-edge-components-versions)

* [Agent Proxy](#agent-proxy)
* [Agent Controller](#agent-controller)
* [Agent Injector](#agent-injector)
* [Aembit CLI](#aembit-cli)
* [Aembit Secrets Operator](#aembit-secrets-operator)
* [Init sidecar container](#init-sidecar-container)
* [MCP Identity Gateway](#mcp-identity-gateway)

[**Aembit packages**](#supported-package-versions)

* [ECS Terraform](#ecs-terraform)
* [Helm chart](#helm-chart)
* [Aembit Secrets Operator Helm chart](#aembit-secrets-operator-helm-chart)
* [Lambda Extension](#lambda-extension)
* [Lambda Layer](#lambda-layer)

## Supported Edge Components versions

The following matrices list the Edge Component versions that Aembit supports along with their release dates.

### Agent Proxy

| Agent Proxy Version   | Release Date | Platforms                     | Notes                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| --------------------- | ------------ | ----------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.34.5755             | 8/27/2026    | Linux (amd64) Windows (amd64) | Honor the gRPC keep-alive properties in the Windows installer; add the authorization chain to workload events; keep the outcome fields on Aembit-generated response events; validate arguments in the Linux installer                                                                                                                                                                                                                       |
| 1.32.4999             | 6/4/2026     | Linux (amd64) Windows (amd64) | Add gRPC keep-alive and HTTP/2 ping options, with keep-alive settings in the Linux and Windows installers; add the HTTP idle timeout setting to the Windows installer; add a private key rotation period for x509 credentials; resolve all directive variables in a single GetCredentials call; restore AGENT\_TRUST\_PATH; handle dmidecode errors when gathering the serial number; include the error body when a server connection fails |
| 1.31.4798             | 5/21/2026    | Linux (amd64) Windows (amd64) | Add support for x509/mTLS client credential injection                                                                                                                                                                                                                                                                                                                                                                                       |
| 1.31.4764             | 5/2/2026     | Linux (amd64) Windows (amd64) | Remove S3 upload size restriction; add support for gathering dynamic claims from environment variables                                                                                                                                                                                                                                                                                                                                      |
| 1.31.4670             | 4/17/2026    | Linux (amd64) Windows (amd64) | Add upstream HTTP proxy support                                                                                                                                                                                                                                                                                                                                                                                                             |
| 1.30.4433             | 3/11/2026    | Linux (amd64) Windows (amd64) | Apply stability improvements for S3 uploads and downloads                                                                                                                                                                                                                                                                                                                                                                                   |
| 1.29.4298             | 2/26/2026    | Linux (amd64) Windows (amd64) | Extend Oracle protocol support with username injection for thick and thin clients and Oracle events; add the command line to process assessments; cache process assessments with a file hash cache; skip the TCP connection handler when process identification is disabled; add logging for process assessments                                                                                                                            |
| 1.28.4063             | 1/16/2026    | Linux (amd64) Windows (amd64) | Add initial Oracle database protocol support with O5LOGON authentication; add eBPF-based network request logging; compute payload signatures on streaming bodies; redact OIDC tokens in logs; fix Lambda pause detection activating when it should not; share the conntrack netlink connection across assessments                                                                                                                           |
| 1.27.3865             | 12/4/2025    | Linux (amd64) Windows (amd64) | Support multiple AWS STS Credential Providers in a single Access Policy via Access Key ID mapping                                                                                                                                                                                                                                                                                                                                           |
| 1.26.3639             | 10/21/2025   | Linux (amd64) Windows (amd64) | Gather Kubernetes environment variable assessments only on Kubernetes; reduce CPU use when sending events; set permissions on parent directories in the VM installer; apply base image security updates                                                                                                                                                                                                                                     |
| 1.25.3600             | 10/2/2025    | Linux (amd64) Windows (amd64) | Apply a security fix to the container base-images                                                                                                                                                                                                                                                                                                                                                                                           |
| 1.25.3494             | 8/22/2025    | Linux (amd64) Windows (amd64) | Add support for the generic OIDC Trust Provider; download the tenant root CA certificate at startup; collect additional network interface information in assessments; determine User-Agent strings at runtime; remove the cloud location override; reject 0 for certain startup arguments                                                                                                                                                   |
| 1.24.3324             | 7/22/2025    | Linux (amd64) Windows (amd64) | Add an in-proxy network diagnostic tool; add raw IP address support; standardize timing environment variables on a \_SECS suffix; make the container start script honor AEMBIT\_LOG\_LEVEL; remove the process command line assessment; fix a token expiration threshold bug; fix the VM installer setting steering hosts twice                                                                                                             |
| 1.23.3002             | 5/26/2025    | Linux (amd64) Windows (amd64) | Fetch STS data only on ECS and Lambda; omit the tenant root CA certificate when loading the bundle; report the build version to Aembit Cloud                                                                                                                                                                                                                                                                                                |
| 1.22.2905             | 4/21/2025    | Linux (amd64) Windows (amd64) | Validate JWT signatures and issuers, with trusted issuers as a startup argument; use the original destination when connecting; fix explicit steering host matching; patch base image vulnerabilities                                                                                                                                                                                                                                        |
| 1.21.2789             | 4/3/2025     | Linux (amd64) Windows (amd64) | Add Kerberos and Prometheus support to the Windows installer; upgrade AWS configuration handling                                                                                                                                                                                                                                                                                                                                            |
| 1.21.2714             | 3/5/2025     | Linux (amd64)                 | Fix an error when calculating the expiration time of an empty certificate                                                                                                                                                                                                                                                                                                                                                                   |
| 1.21.2696             | 3/3/2025     | Linux (amd64) Windows (amd64) | Add AWS SigV4A signing; remove the KMS constraint from AWS SigV4 signatures; fix the immediate termination shutdown strategy                                                                                                                                                                                                                                                                                                                |
| 1.21.2670             | 2/20/2025    | Linux (amd64) Windows (amd64) | Add Vault login support                                                                                                                                                                                                                                                                                                                                                                                                                     |
| 1.20.2559             | 1/28/2025    | Windows (amd64)               | Initial Windows Server release of Agent Proxy                                                                                                                                                                                                                                                                                                                                                                                               |
| 1.19.2439             | 12/26/2024   | Linux (amd64)                 | Add SELinux support to the VM installer with RHEL rulesets; add a Windows build with host assessments and an MSI installer; fix constant reloading of native certificates; fix a DNS resolution race; add an option to disable the directive cache; log startup arguments                                                                                                                                                                   |
| 1.18.2265             | 10/29/2024   | Linux (amd64)                 |                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| 1.18.2262             | 10/22/2024   | Linux (amd64)                 | Remove the expiration on the VM service user; rename the steering methods; exclude the AWS instance metadata address from steering rules                                                                                                                                                                                                                                                                                                    |
| 1.17.2255             | 10/8/2024    | Linux (amd64)                 | Add Prometheus metrics for connections, credential injection, process statistics, and control plane status, with a port override and an option to disable the endpoint; gather only the assessments that apply to the deployment model                                                                                                                                                                                                      |
| 1.17.2169             | 9/17/2024    | Linux (amd64)                 | Add a rolling network trace; add the virtual appliance deployment model; enforce TLS in the container start script and add TLS debug logging; add metrics plumbing                                                                                                                                                                                                                                                                          |
| 1.17.2155             | 9/5/2024     | Linux (amd64)                 | Add an HTTP idle timeout; consolidate token refresh handling                                                                                                                                                                                                                                                                                                                                                                                |
| 1.16.2139             | 8/27/2024    | Linux (amd64)                 | Collect information for multiple Credential Providers; shut down on errors in critical tasks; fix HTTP data collection on requests without a JSON body; update the log level environment variable                                                                                                                                                                                                                                           |
| 1.15.2093             | 7/3/2024     | Linux (amd64)                 | Steer only specific traffic to Agent Proxy, including Docker containers, with custom steering; add an edge monitor script; add a registration timeout; replace AEMBIT\_AGENT\_VM\_INSTALL with AEMBIT\_AGENT\_PROXY\_DEPLOYMENT\_MODEL; fix root certificates loading twice at boot                                                                                                                                                         |
| 1.14.1980             | 6/19/2024    | Linux (amd64)                 |                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| 1.14.1959             | 5/30/2024    | Linux (amd64)                 | Raise the open files limit on VM installs; remove OpenSSL usage; lower the log level when STS environment variables are missing                                                                                                                                                                                                                                                                                                             |
| 1.14.1913             | 5/14/2024    | Linux (amd64)                 | Add AWS Role Trust Provider support for EC2; do TCP passthrough over HTTP when TLS Decrypt is not set; add the resource set ID; fix a Lambda event-flush race; install the journald namespace config only on systemd newer than 245                                                                                                                                                                                                         |
| 1.13.1851             | 4/19/2024    | Linux (amd64)                 | Serve health data on the health endpoint separately from the service server; add the Lambda ARN Client Workload identifier with paused-container checks; add CNAME support; flush events on immediate termination; statically link the binaries against musl                                                                                                                                                                                |
| 1.13.1818             | 4/8/2024     | Linux (amd64)                 | Listen on all local addresses for DNS proxying; add protocol identification; add a health port                                                                                                                                                                                                                                                                                                                                              |
| 1.12.1699             | 3/12/2024    | Linux (amd64)                 | Terminate immediately on SIGTERM; merge the iptables and firewalld rule scripts; fix Kerberos calls over TLS; allow upgrades without redefining environment variables; lower the log level of routine messages                                                                                                                                                                                                                              |
| 1.12.1621             | 2/29/2024    | Linux (amd64)                 | Add Kerberos attestation through Agent Controller with keytab and TGT support in the VM installer; fix DNS messages larger than 512 bytes failing to resolve; allow an https Agent Controller address in the installer; cache Kerberos assessments                                                                                                                                                                                          |
| 1.11.1551             | 2/8/2024     | Linux (amd64)                 | Resolve multiple credentials in one request; make the HTTP server port configurable; add firewalld support to the VM installer; check for the iptables dependency at install; build against an older glibc; add the Server Workload to TCP passthrough events; add a timeout when fetching tokens from Agent Controller                                                                                                                     |
| 1.10.1441             | 1/4/2024     | Linux (amd64)                 | Validate process identification with conntrack events; fetch Redis credentials just in time; add TTL information when caching credentials; add a backoff interval for command queue polling                                                                                                                                                                                                                                                 |
| 1.10.1415             | 12/7/2023    | Linux (amd64)                 | Add Azure attestation support; add the system serial number and endpoint security agent data to assessments; add a sudoers file and harden file permissions in the VM installer; bundle AWS certificates in the container; include a message in events when directive retrieval fails                                                                                                                                                       |
| 1.9.1337              | 11/8/2023    | Linux (amd64)                 |                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| Show 34 more versions |              |                               |                                                                                                                                                                                                                                                                                                                                                                                                                                             |

### Agent Controller

| Agent Controller Version | Release Date | Platforms                     | Notes                                                                                                                                                                                                                                                                                                                                                                 |
| ------------------------ | ------------ | ----------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.34.3725                | 9/7/2026     | Linux (amd64) Windows (amd64) | Fix Kerberos attestation on AWS EC2 instances; install intermediate CA certificates into the Windows LocalMachine store for TLS chain validation; keep log file access when reinstalling on Windows with a different service logon account; fix TLS certificate status reporting during renewal; update the container base image to start on newer container runtimes |
| 1.32.3541                | 7/8/2026     | Linux (amd64) Windows (amd64) | Harden the container image and add an embedded SBOM                                                                                                                                                                                                                                                                                                                   |
| 1.32.3502                | 6/4/2026     | Linux (amd64) Windows (amd64) | Add fallback health reporting when a proxy blocks the command stream; reconnect the gRPC channel automatically after network or proxy drops; fix TLS certificate renewal stalling on Kubernetes after a file-write error; stop retrying dropped command streams with expired tokens                                                                                   |
| 1.30.3384                | 4/15/2026    | Linux (amd64) Windows (amd64) | Add support for HTTP proxy environment variables                                                                                                                                                                                                                                                                                                                      |
| 1.30.3336                | 3/11/2026    | Linux (amd64) Windows (amd64) | Improve compatibility across Kubernetes platforms including Red Hat OpenShift (ROSA)                                                                                                                                                                                                                                                                                  |
| 1.29.3239                | 2/26/2026    | Linux (amd64) Windows (amd64) | Stay registered when access token renewal with a refresh token fails; default the log level to Info; batch Kubernetes Secret token updates into a single patch; disable the Prometheus TLS endpoint on Kubernetes; upgrade to .NET 10 and sign container images with cosign                                                                                           |
| 1.27.2906                | 11/25/2025   | Linux (amd64) Windows (amd64) | Apply bug fixes and logging improvements                                                                                                                                                                                                                                                                                                                              |
| 1.25.2622                | 9/9/2025     | Linux (amd64) Windows (amd64) | Deprecate AEMBIT\_HTTP\_PORT\_DISABLED with a warning log; remove HSTS middleware; speed up startup attestation on EKS Fargate and fix ECS Fargate credential handling                                                                                                                                                                                                |
| 1.24.2485                | 7/29/2025    | Linux (amd64) Windows (amd64) | Sign the container image with cosign; bind TLS ports when metrics and HTTP are both disabled; close the Prometheus HTTP port when customer-managed TLS is enabled; log AEMBIT\_HTTP\_PORT\_DISABLED at startup; apply a security hardening fix                                                                                                                        |
| 1.23.2263                | 6/11/2025    | Linux (amd64) Windows (amd64) | Log environment variables at startup; add logging for TLS certificate retrieval from Aembit Cloud; unify reporting of Aembit-managed and customer-managed TLS certificates to Aembit Cloud                                                                                                                                                                            |
| 1.23.2160                | 6/2/2025     | Linux (amd64) Windows (amd64) | Add customer-managed TLS support to the Linux installer; add AEMBIT\_HTTP\_PORT\_DISABLED to the Windows installer and default its log level to warning; fix Windows upgrades that specify a different service user; listen only on the TLS port on VMs when TLS is enabled                                                                                           |
| 1.21.2101                | 4/4/2025     | Windows (amd64)               | Windows only                                                                                                                                                                                                                                                                                                                                                          |
| 1.21.1914                | 2/27/2025    | Linux (amd64)                 | Serve the full CA chain when TLS is enabled; fix unhealthy health status on ECS; fix log file access in HA configurations; upgrade to .NET 9                                                                                                                                                                                                                          |
| 1.19.1752                | 12/26/2024   | Linux (amd64)                 | Add SELinux support to the VM installer; report health status to Aembit Cloud on connect and disconnect; add standardized log levels including trace; log lifecycle changes and tasks; quiet exceptions during Trust Provider registration                                                                                                                            |
| 1.18.1602                | 10/22/2024   | Linux (amd64)                 | Fix the VM installer creating a service account that could expire; handle an HTTP protocol exception in the cloud connection                                                                                                                                                                                                                                          |
| 1.17.1579                | 10/8/2024    | Linux (amd64)                 | Make the log level configurable on Kubernetes; remove deprecated environment variables                                                                                                                                                                                                                                                                                |
| 1.17.1533                | 9/30/2024    | Linux (amd64)                 | Add Prometheus metrics collection with a setting to enable or disable it                                                                                                                                                                                                                                                                                              |
| 1.17.1518                | 9/17/2024    | Linux (amd64)                 | Report health to Aembit Cloud and receive commands over a persistent connection; add Aembit-managed TLS certificates for Agent Proxy with automatic reload and renewal; fix Healthy status while the cloud connection is down; fail early when neither device code nor Trust Provider is configured; upgrade to .NET 8                                                |
| 1.16.1341                | 7/29/2024    | Linux (amd64)                 | ECS Only                                                                                                                                                                                                                                                                                                                                                              |
| 1.14.1074                | 5/10/2024    | Linux (amd64)                 | Fix an overnight crash loop; fix missing actor and user agent in audit log entries for attestation and registration; support reinstalling without TLS after a TLS install; skip the journald namespace config on systemd older than 245                                                                                                                               |
| 1.12.974                 | 3/20/2024    | Linux (amd64)                 | Renew the Kerberos attestation certificate in the background; send TLS certificate metadata to Aembit Cloud; refuse Kerberos attestation documents when the certificate has expired                                                                                                                                                                                   |
| 1.12.927                 | 3/12/2024    | Linux (amd64)                 | Add configurable AEMBIT\_LOG\_LEVEL; fix the service showing failed after stop on Red Hat; log when registration token retrieval for Agent Proxy fails; validate the Kerberos attestation installer variable                                                                                                                                                          |
| 1.12.878                 | 2/29/2024    | Linux (amd64)                 | Add an HTTPS endpoint on port 5443 with a VM installer option and Kubernetes TLS Secret support for the certificate; add Kerberos attestation signing for Agent Proxy; return the agent access token in Registered state instead of 503                                                                                                                               |
| 1.11.742                 | 1/23/2024    | Linux (amd64)                 | Change status to Unregistered when authentication starts failing; handle switching from device code to Trust Provider authentication; support RHEL 8.6                                                                                                                                                                                                                |
| 1.9.696                  | 11/13/2023   | Linux (amd64)                 |                                                                                                                                                                                                                                                                                                                                                                       |
| Show 20 more versions    |              |                               |                                                                                                                                                                                                                                                                                                                                                                       |

### Agent Injector

| Agent Injector Version | Release Date | Notes                                                                   |
| ---------------------- | ------------ | ----------------------------------------------------------------------- |
| 1.34.433               | 8/27/2026    | Move to a newer Debian base image; fix RUSTSEC-2026-0185 in quinn-proto |
| 1.31.425               | 5/2/2026     | Apply security upgrades and bug fixes                                   |
| 1.29.394               | 2/26/2026    |                                                                         |
| 1.26.353               | 10/21/2025   |                                                                         |
| 1.25.329               | 10/2/2025    | Apply a security fix to the container base-images                       |
| 1.23.295               | 5/30/2025    |                                                                         |
| 1.18.259               | 10/23/2024   |                                                                         |
| 1.17.234               | 10/8/2024    |                                                                         |
| 1.17.198               | 9/20/2024    |                                                                         |
| 1.14.190               | 5/30/2024    |                                                                         |
| 1.14.182               | 4/30/2024    |                                                                         |
| 1.9.142                | 10/16/2023   |                                                                         |
| 1.8.137                | 9/27/2023    |                                                                         |
| Show 8 more versions   |              |                                                                         |

### Aembit CLI

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

### Aembit Secrets Operator

| Aembit Secrets Operator Version | Release Date | Platforms            | Notes                                                     |
| ------------------------------- | ------------ | -------------------- | --------------------------------------------------------- |
| 1.34.358                        | 8/31/2026    | Linux (amd64, arm64) | Add X.509-SVID Credential Provider support                |
| 1.32.322                        | 6/12/2026    | Linux (amd64, arm64) | Add support for the remaining Aembit Credential Providers |
| 1.31.314                        | 5/15/2026    | Linux (amd64, arm64) | Graduate CRDs from aembit.io/v1beta1 to aembit.io/v1      |
| 1.31.298                        | 5/12/2026    | Linux (amd64, arm64) | Initial release                                           |

### Init sidecar container

| Init sidecar container Version | Release Date | Notes                                             |
| ------------------------------ | ------------ | ------------------------------------------------- |
| 1.34.138                       | 8/27/2026    | Move to a newer Debian base image                 |
| 1.25.130                       | 10/2/2025    | Apply a security fix to the container base-images |
| 1.25.127                       | 8/22/2025    |                                                   |
| 1.18.92                        | 1/14/2025    |                                                   |
| 1.14.86                        | 5/30/2024    |                                                   |
| 1.13.77                        | 4/19/2024    |                                                   |
| 1.8.43                         | 9/27/2023    |                                                   |
| 1.7.37                         | 8/14/2023    |                                                   |
| 1.0.30                         | 2/10/2023    |                                                   |
| Show 4 more versions           |              |                                                   |

### MCP Identity Gateway

| MCP Identity Gateway Version | Release Date | Platforms            | Notes                                                                                                                                                                                                                 |
| ---------------------------- | ------------ | -------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.34.5794                    | 9/15/2026    | Linux (amd64, arm64) | MCP Tool Access Control enforcement and security updates                                                                                                                                                              |
| 1.34.5733                    | 8/18/2026    | Linux (amd64)        | Configurable fanout timeouts and nine new Prometheus metrics                                                                                                                                                          |
| 1.33.5654                    | 8/7/2026     | Linux (amd64)        | Broader Content Security inspection coverage and a single workload event per inspection decision                                                                                                                      |
| 1.33.5547                    | 7/28/2026    | Linux (amd64)        | Separate liveness and readiness health endpoints and upstream reconnection improvements                                                                                                                               |
| 1.32.5098                    | 6/30/2026    | Linux (amd64)        | Workload event correlation with Client Workload and Server Workload identity in events, session IDs bound to the authenticated user, and optional session persistence across restarts                                 |
| 1.32.5006                    | 6/4/2026     | Linux (amd64)        | MCP protocol ping support and full authorization chain in workload events                                                                                                                                             |
| 1.31.4955                    | 5/19/2026    | Linux (amd64)        | Session deletion, MCP-level error metrics, and application-specific Prometheus metrics                                                                                                                                |
| 1.30.4549                    | 4/3/2026     | Linux (amd64)        | Auth-first enforcement, tool annotations, metrics endpoint, session management improvements, and Claude compatibility fixes                                                                                           |
| 1.30.4542                    | 3/26/2026    | Linux (amd64)        | Route tools/call after a gateway restart for clients that reuse a cached tool list                                                                                                                                    |
| 1.30.4530                    | 3/25/2026    | Linux (amd64)        | Skip resource fanout for MCP servers that do not support resources; omit null \_meta values in tool responses for Claude compatibility; add a WWW-Authenticate header with the resource metadata URL to 401 responses |
| 1.29.4419                    | 3/19/2026    | Linux (amd64)        | Add MCP resource support (resources/list and resources/read)                                                                                                                                                          |
| 1.28.4136                    | 1/28/2026    | Linux (amd64)        | Initial limited beta release                                                                                                                                                                                          |
| Show 7 more versions         |              |                      |                                                                                                                                                                                                                       |

## Supported package versions

The following matrices list the package versions that Aembit supports along with their release dates.

### ECS Terraform

| ECS Terraform Version | Release Date | Notes                                                                                             |
| --------------------- | ------------ | ------------------------------------------------------------------------------------------------- |
| 1.34.1                | 8/27/2026    |                                                                                                   |
| 1.32.1                | 7/8/2026     |                                                                                                   |
| 1.32.0                | 6/4/2026     |                                                                                                   |
| 1.31.3                | 5/21/2026    |                                                                                                   |
| 1.31.1                | 5/2/2026     |                                                                                                   |
| 1.31.0                | 4/17/2026    | Add upstream HTTP proxy support                                                                   |
| 1.30.0                | 3/11/2026    |                                                                                                   |
| 1.28.0                | 1/16/2026    |                                                                                                   |
| 1.27.1                | 12/4/2025    | Support multiple AWS STS Credential Providers in a single Access Policy via Access Key ID mapping |
| 1.27.0                | 11/25/2025   | Apply bug fixes and logging improvements                                                          |
| 1.26.1                | 10/21/2025   |                                                                                                   |
| 1.26.0                | 10/2/2025    | Apply a security fix to the container base-images                                                 |
| 1.25.2                | 9/9/2025     |                                                                                                   |
| 1.25.1                | 8/22/2025    |                                                                                                   |
| 1.24.2                | 7/29/2025    |                                                                                                   |
| 1.24.1                | 7/22/2025    |                                                                                                   |
| 1.23.4                | 6/11/2025    |                                                                                                   |
| 1.23.3                | 6/2/2025     |                                                                                                   |
| 1.23.1                | 5/26/2025    |                                                                                                   |
| 1.23.0                | 5/21/2025    |                                                                                                   |
| 1.22.1                | 4/21/2025    |                                                                                                   |
| 1.21.4                | 4/3/2025     |                                                                                                   |
| 1.21.3                | 3/5/2025     |                                                                                                   |
| 1.21.1                | 2/27/2025    |                                                                                                   |
| 1.21.0                | 2/20/2025    |                                                                                                   |
| 1.20.0                | 12/26/2024   |                                                                                                   |
| 1.17.4                | 10/08/2024   |                                                                                                   |
| 1.17.3                | 9/30/2024    |                                                                                                   |
| 1.17.1                | 9/17/2024    |                                                                                                   |
| 1.17.0                | 9/5/2024     |                                                                                                   |
| 1.16.2                | 8/27/2024    |                                                                                                   |
| 1.16.0                | 8/11/2024    |                                                                                                   |
| 1.15.0                | 6/19/2024    |                                                                                                   |
| 1.14.3                | 5/30/2024    |                                                                                                   |
| 1.14.2                | 5/14/2024    |                                                                                                   |
| 1.14.1                | 5/10/2024    |                                                                                                   |
| 1.13.2                | 4/19/2024    |                                                                                                   |
| 1.13.1                | 4/8/2024     |                                                                                                   |
| 1.13.0                | 3/20/2024    |                                                                                                   |
| 1.12.2                | 3/12/2024    |                                                                                                   |
| 1.12.1                | 2/29/2024    |                                                                                                   |
| 1.12.0                | 2/8/2024     |                                                                                                   |
| Show 37 more versions |              |                                                                                                   |

### Helm chart

| Helm chart Version    | Release Date | Notes                                                                                             |
| --------------------- | ------------ | ------------------------------------------------------------------------------------------------- |
| 1.34.566              | 8/27/2026    |                                                                                                   |
| 1.32.562              | 7/8/2026     |                                                                                                   |
| 1.32.557              | 6/4/2026     |                                                                                                   |
| 1.31.555              | 5/21/2026    |                                                                                                   |
| 1.31.553              | 5/2/2026     |                                                                                                   |
| 1.31.543              | 4/17/2026    | Add upstream HTTP proxy support                                                                   |
| 1.30.531              | 3/11/2026    | Improve Helm Chart compatibility across Kubernetes platforms                                      |
| 1.29.518              | 2/26/2026    |                                                                                                   |
| 1.28.507              | 1/16/2026    |                                                                                                   |
| 1.27.505              | 12/4/2025    | Support multiple AWS STS Credential Providers in a single Access Policy via Access Key ID mapping |
| 1.27.503              | 11/25/2025   | Apply bug fixes and logging improvements                                                          |
| 1.26.500              | 10/21/2025   |                                                                                                   |
| 1.26.498              | 10/2/2025    | Apply a security fix to the container base-images                                                 |
| 1.25.496              | 9/9/2025     |                                                                                                   |
| 1.25.494              | 8/22/2025    |                                                                                                   |
| 1.24.428              | 7/29/2025    |                                                                                                   |
| 1.24.421              | 7/22/2025    |                                                                                                   |
| 1.23.405              | 6/11/2025    |                                                                                                   |
| 1.23.3                | 6/2/2025     |                                                                                                   |
| 1.23.401              | 5/30/2025    |                                                                                                   |
| 1.23.394              | 5/26/2025    |                                                                                                   |
| 1.22.364              | 4/21/2025    |                                                                                                   |
| 1.21.347              | 4/3/2025     |                                                                                                   |
| 1.21.331              | 3/5/2025     |                                                                                                   |
| 1.21.329              | 3/3/2025     |                                                                                                   |
| 1.21.325              | 2/27/2025    |                                                                                                   |
| 1.21.318              | 2/20/2025    |                                                                                                   |
| 1.20.300              | 1/14/2025    |                                                                                                   |
| 1.20.298              | 12/26/2024   |                                                                                                   |
| 1.18.294              | 10/29/2024   |                                                                                                   |
| 1.18.293              | 10/23/2024   |                                                                                                   |
| 1.17.288              | 10/11/2024   |                                                                                                   |
| 1.17.282              | 10/08/2024   |                                                                                                   |
| 1.17.272              | 9/30/2024    |                                                                                                   |
| 1.17.258              | 9/20/2024    |                                                                                                   |
| 1.17.254              | 9/17/2024    |                                                                                                   |
| 1.17.246              | 9/5/2024     |                                                                                                   |
| 1.16.238              | 8/11/2024    |                                                                                                   |
| 1.15.220              | 6/19/2024    |                                                                                                   |
| 1.14.211              | 5/30/2024    |                                                                                                   |
| 1.14.205              | 5/14/2024    |                                                                                                   |
| 1.14.204              | 5/10/2024    |                                                                                                   |
| 1.14.194              | 4/30/2024    |                                                                                                   |
| 1.13.190              | 4/19/2024    |                                                                                                   |
| 1.13.181              | 3/19/2024    |                                                                                                   |
| 1.12.176              | 3/12/2024    |                                                                                                   |
| 1.12.174              | 2/29/2024    |                                                                                                   |
| 1.12.164              | 2/9/2024     |                                                                                                   |
| Show 43 more versions |              |                                                                                                   |

### Aembit Secrets Operator Helm chart

| Aembit Secrets Operator Helm chart Version | Release Date | Notes                                                     |
| ------------------------------------------ | ------------ | --------------------------------------------------------- |
| 1.34.358                                   | 8/31/2026    | Add X.509-SVID Credential Provider support                |
| 1.32.322                                   | 6/12/2026    | Add support for the remaining Aembit Credential Providers |
| 1.31.314                                   | 5/15/2026    | Graduate CRDs from aembit.io/v1beta1 to aembit.io/v1      |
| 1.31.298                                   | 5/12/2026    | Initial release                                           |

### Lambda Extension

| Lambda Extension Version | Release Date | Notes                                                                                             |
| ------------------------ | ------------ | ------------------------------------------------------------------------------------------------- |
| 1.34.175                 | 8/27/2026    | Update the Go version                                                                             |
| 1.32.168                 | 6/4/2026     |                                                                                                   |
| 1.31.164                 | 5/21/2026    |                                                                                                   |
| 1.31.162                 | 5/2/2026     |                                                                                                   |
| 1.31.160                 | 4/17/2026    | Add upstream HTTP proxy support                                                                   |
| 1.29.157                 | 2/26/2026    |                                                                                                   |
| 1.28.151                 | 1/16/2026    |                                                                                                   |
| 1.27.147                 | 12/4/2025    | Support multiple AWS STS Credential Providers in a single Access Policy via Access Key ID mapping |
| 1.26.143                 | 10/21/2025   |                                                                                                   |
| 1.26.139                 | 10/2/2025    | Apply a security fix to the container base-images                                                 |
| 1.25.132                 | 9/2/2025     |                                                                                                   |
| 1.24.128                 | 7/22/2025    |                                                                                                   |
| 1.23.112                 | 5/26/2025    |                                                                                                   |
| 1.22.74                  | 4/21/2025    |                                                                                                   |
| 1.21.66                  | 4/3/2025     |                                                                                                   |
| 1.21.65                  | 3/5/2025     |                                                                                                   |
| 1.21.64                  | 3/3/2025     |                                                                                                   |
| 1.21.62                  | 2/20/2025    |                                                                                                   |
| 1.19.56                  | 12/26/2024   |                                                                                                   |
| 1.18.53                  | 11/22/2024   |                                                                                                   |
| 1.17.48                  | 10/08/2024   |                                                                                                   |
| 1.17.46                  | 9/17/2024    |                                                                                                   |
| 1.17.45                  | 9/5/2024     |                                                                                                   |
| 1.16.43                  | 6/19/2024    |                                                                                                   |
| 1.14.38                  | 6/19/2024    |                                                                                                   |
| 1.14.37                  | 5/31/2024    |                                                                                                   |
| 1.14.29                  | 4/22/2024    |                                                                                                   |
| Show 22 more versions    |              |                                                                                                   |

### Lambda Layer

| Lambda Layer Version | Release Date | Notes                                                                                             |
| -------------------- | ------------ | ------------------------------------------------------------------------------------------------- |
| 1.32.168             | 6/4/2026     |                                                                                                   |
| 1.31.164             | 5/21/2026    |                                                                                                   |
| 1.31.162             | 5/2/2026     |                                                                                                   |
| 1.31.160             | 4/17/2026    | Add upstream HTTP proxy support                                                                   |
| 1.29.157             | 2/26/2026    |                                                                                                   |
| 1.28.151             | 1/16/2026    |                                                                                                   |
| 1.27.147             | 12/4/2025    | Support multiple AWS STS Credential Providers in a single Access Policy via Access Key ID mapping |
| 1.26.143             | 10/21/2025   |                                                                                                   |
| 1.26.139             | 10/2/2025    | Apply a security fix to the container base-images                                                 |
| 1.25.132             | 9/2/2025     |                                                                                                   |
| 1.23.112             | 5/26/2025    |                                                                                                   |
| 1.22.110             | 5/6/2025     |                                                                                                   |
| Show 7 more versions |              |                                                                                                   |
