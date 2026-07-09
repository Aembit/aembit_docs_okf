---
type: reference
title: "Edge Component Supported Versions"
description: "Supported versions and release dates for Aembit Edge Components and packages"
resource: https://docs.aembit.io/reference/edge-components/edge-component-supported-versions/
tags: [edge-component]
timestamp: 2026-06-03T15:12:48-04:00
type_inferred: true
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

[**Aembit packages**](#supported-package-versions)

* [ECS Terraform](#ecs-terraform)
* [Helm chart](#helm-chart)
* [Aembit Secrets Operator Helm chart](#aembit-secrets-operator-helm-chart)
* [Lambda Extension](#lambda-extension)
* [Lambda Layer](#lambda-layer)
* [Virtual appliance](#virtual-appliance)

## Supported Edge Components versions

[Section titled “Supported Edge Components versions”](#supported-edge-components-versions)

The following matrices list the Agent Proxy, Agent Controller, Agent Injector, Aembit CLI, Aembit Secrets Operator, and Init Sidecar Container Edge Component versions that Aembit supports along with their release dates.

### Agent Proxy

[Section titled “Agent Proxy”](#agent-proxy)

| Agent Proxy Version   | Release Date | Platforms                     | Notes                                                                                                  |
| --------------------- | ------------ | ----------------------------- | ------------------------------------------------------------------------------------------------------ |
| 1.32.4999             | 6/4/2026     | Linux (amd64) Windows (amd64) |                                                                                                        |
| 1.31.4798             | 5/21/2026    | Linux (amd64) Windows (amd64) | Add support for x509/mTLS client credential injection                                                  |
| 1.31.4764             | 5/2/2026     | Linux (amd64) Windows (amd64) | Remove S3 upload size restriction; add support for gathering dynamic claims from environment variables |
| 1.31.4670             | 4/17/2026    | Linux (amd64) Windows (amd64) | Add upstream HTTP proxy support                                                                        |
| 1.30.4433             | 3/11/2026    | Linux (amd64) Windows (amd64) | Apply stability improvements for S3 uploads and downloads                                              |
| 1.29.4298             | 2/26/2026    | Linux (amd64) Windows (amd64) |                                                                                                        |
| 1.28.4063             | 1/16/2026    | Linux (amd64) Windows (amd64) |                                                                                                        |
| 1.27.3865             | 12/4/2025    | Linux (amd64) Windows (amd64) | Support multiple AWS STS Credential Providers in a single Access Policy via Access Key ID mapping      |
| 1.26.3639             | 10/21/2025   | Linux (amd64) Windows (amd64) |                                                                                                        |
| 1.25.3600             | 10/2/2025    | Linux (amd64) Windows (amd64) | Apply a security fix to the container base-images                                                      |
| 1.25.3494             | 8/22/2025    | Linux (amd64) Windows (amd64) |                                                                                                        |
| 1.24.3324             | 7/22/2025    | Linux (amd64) Windows (amd64) |                                                                                                        |
| 1.23.3002             | 5/26/2025    | Linux (amd64) Windows (amd64) |                                                                                                        |
| 1.22.2905             | 4/21/2025    | Linux (amd64) Windows (amd64) |                                                                                                        |
| 1.21.2789             | 4/3/2025     | Linux (amd64) Windows (amd64) |                                                                                                        |
| 1.21.2714             | 3/5/2025     | Linux (amd64)                 |                                                                                                        |
| 1.21.2696             | 3/3/2025     | Linux (amd64) Windows (amd64) |                                                                                                        |
| 1.21.2670             | 2/20/2025    | Linux (amd64) Windows (amd64) |                                                                                                        |
| 1.20.2559             | 1/28/2025    | Windows (amd64)               | - Windows only                                                                                         |
| 1.19.2439             | 12/26/2024   | Linux (amd64)                 |                                                                                                        |
| 1.18.2265             | 10/29/2024   | Linux (amd64)                 |                                                                                                        |
| 1.18.2262             | 10/22/2024   | Linux (amd64)                 |                                                                                                        |
| 1.17.2255             | 10/8/2024    | Linux (amd64)                 |                                                                                                        |
| 1.17.2169             | 9/17/2024    | Linux (amd64)                 |                                                                                                        |
| 1.17.2155             | 9/5/2024     | Linux (amd64)                 |                                                                                                        |
| 1.16.2139             | 8/27/2024    | Linux (amd64)                 |                                                                                                        |
| 1.14.1980             | 6/19/2024    | Linux (amd64)                 |                                                                                                        |
| 1.14.1959             | 5/30/2024    | Linux (amd64)                 |                                                                                                        |
| 1.14.1913             | 5/14/2024    | Linux (amd64)                 |                                                                                                        |
| 1.13.1851             | 4/19/2024    | Linux (amd64)                 |                                                                                                        |
| 1.13.1818             | 4/8/2024     | Linux (amd64)                 |                                                                                                        |
| 1.12.1699             | 3/12/2024    | Linux (amd64)                 |                                                                                                        |
| 1.12.1621             | 2/29/2024    | Linux (amd64)                 |                                                                                                        |
| 1.11.1551             | 2/8/2024     | Linux (amd64)                 |                                                                                                        |
| Show 29 more versions |              |                               |                                                                                                        |

### Agent Controller

[Section titled “Agent Controller”](#agent-controller)

| Agent Controller Version | Release Date | Platforms                     | Notes                                                                                |
| ------------------------ | ------------ | ----------------------------- | ------------------------------------------------------------------------------------ |
| 1.32.3502                | 6/4/2026     | Linux (amd64) Windows (amd64) |                                                                                      |
| 1.30.3384                | 4/15/2026    | Linux (amd64) Windows (amd64) | Add support for HTTP proxy environment variables                                     |
| 1.30.3336                | 3/11/2026    | Linux (amd64) Windows (amd64) | Improve compatibility across Kubernetes platforms including Red Hat OpenShift (ROSA) |
| 1.29.3239                | 2/26/2026    | Linux (amd64) Windows (amd64) |                                                                                      |
| 1.27.2906                | 11/25/2025   | Linux (amd64) Windows (amd64) | Apply bug fixes and logging improvements                                             |
| 1.25.2622                | 9/9/2025     | Linux (amd64) Windows (amd64) |                                                                                      |
| 1.24.2485                | 7/29/2025    | Linux (amd64) Windows (amd64) |                                                                                      |
| 1.23.2263                | 6/11/2025    | Linux (amd64) Windows (amd64) |                                                                                      |
| 1.23.2160                | 6/2/2025     | Linux (amd64) Windows (amd64) |                                                                                      |
| 1.21.2101                | 4/4/2025     | Windows (amd64)               | Windows only                                                                         |
| 1.21.1914                | 2/27/2025    | Linux (amd64)                 |                                                                                      |
| 1.19.1752                | 12/26/2024   | Linux (amd64)                 |                                                                                      |
| 1.18.1602                | 10/22/2024   | Linux (amd64)                 |                                                                                      |
| 1.17.1579                | 10/8/2024    | Linux (amd64)                 |                                                                                      |
| 1.17.1533                | 9/30/2024    | Linux (amd64)                 |                                                                                      |
| 1.17.1518                | 9/17/2024    | Linux (amd64)                 |                                                                                      |
| 1.16.1341                | 7/29/2024    | Linux (amd64)                 | ECS Only                                                                             |
| 1.14.1074                | 5/10/2024    | Linux (amd64)                 |                                                                                      |
| 1.12.974                 | 3/20/2024    | Linux (amd64)                 |                                                                                      |
| 1.12.927                 | 3/12/2024    | Linux (amd64)                 |                                                                                      |
| 1.12.878                 | 2/29/2024    | Linux (amd64)                 |                                                                                      |
| Show 16 more versions    |              |                               |                                                                                      |

### Agent Injector

[Section titled “Agent Injector”](#agent-injector)

| Agent Injector Version | Release Date | Notes                                             |
| ---------------------- | ------------ | ------------------------------------------------- |
| 1.31.425               | 5/2/2026     | Apply security upgrades and bug fixes             |
| 1.29.394               | 2/26/2026    |                                                   |
| 1.26.353               | 10/21/2025   |                                                   |
| 1.25.329               | 10/2/2025    | Apply a security fix to the container base-images |
| 1.23.295               | 5/30/2025    |                                                   |
| 1.18.259               | 10/23/2024   |                                                   |
| 1.17.234               | 10/8/2024    |                                                   |
| 1.17.198               | 9/20/2024    |                                                   |
| 1.14.190               | 5/30/2024    |                                                   |
| 1.14.182               | 4/30/2024    |                                                   |
| 1.9.142                | 10/16/2023   |                                                   |
| 1.8.137                | 9/27/2023    |                                                   |
| Show 7 more versions   |              |                                                   |

### Aembit CLI

[Section titled “Aembit CLI”](#aembit-cli)

| Aembit CLI Version | Release Date | Platforms                            | Notes                                                                                                                                                                    |
| ------------------ | ------------ | ------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 1.31.4764          | 5/2/2026     | Linux (amd64, arm64) Windows (amd64) | Add upstream HTTP proxy support; add --client-workload-id flag and OIDC token expiration validation; add support for gathering dynamic claims from environment variables |
| 1.24.3328          | 7/29/2025    | Linux (amd64, arm64) Windows (amd64) |                                                                                                                                                                          |

### Aembit Secrets Operator

[Section titled “Aembit Secrets Operator”](#aembit-secrets-operator)

| Aembit Secrets Operator Version | Release Date | Platforms            | Notes                                                     |
| ------------------------------- | ------------ | -------------------- | --------------------------------------------------------- |
| 1.32.322                        | 6/12/2026    | Linux (amd64, arm64) | Add support for the remaining Aembit Credential Providers |
| 1.31.314                        | 5/15/2026    | Linux (amd64, arm64) | Graduate CRDs from aembit.io/v1beta1 to aembit.io/v1      |
| 1.31.298                        | 5/12/2026    | Linux (amd64, arm64) | Initial release                                           |

### Init sidecar container

[Section titled “Init sidecar container”](#init-sidecar-container)

| Init sidecar container Version | Release Date | Notes                                             |
| ------------------------------ | ------------ | ------------------------------------------------- |
| 1.25.130                       | 10/2/2025    | Apply a security fix to the container base-images |
| 1.25.127                       | 8/22/2025    |                                                   |
| 1.18.92                        | 1/14/2025    |                                                   |
| 1.14.86                        | 5/30/2024    |                                                   |
| 1.13.77                        | 4/19/2024    |                                                   |
| 1.8.43                         | 9/27/2023    |                                                   |
| 1.7.37                         | 8/14/2023    |                                                   |
| 1.0.30                         | 2/10/2023    |                                                   |
| Show 3 more versions           |              |                                                   |

## Supported package versions

[Section titled “Supported package versions”](#supported-package-versions)

The following matrices list the ECS Terraform, Helm chart, Aembit Secrets Operator Helm chart, Lambda Layer, Lambda Extension, and Virtual Appliance package versions that Aembit supports along with their release dates.

### ECS Terraform

[Section titled “ECS Terraform”](#ecs-terraform)

| ECS Terraform Version | Release Date | Notes                                                                                             |
| --------------------- | ------------ | ------------------------------------------------------------------------------------------------- |
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
| Show 35 more versions |              |                                                                                                   |

### Helm chart

[Section titled “Helm chart”](#helm-chart)

| Helm chart Version    | Release Date | Notes                                                                                             |
| --------------------- | ------------ | ------------------------------------------------------------------------------------------------- |
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
| Show 41 more versions |              |                                                                                                   |

### Aembit Secrets Operator Helm chart

[Section titled “Aembit Secrets Operator Helm chart”](#aembit-secrets-operator-helm-chart)

| Aembit Secrets Operator Helm chart Version | Release Date | Notes                                                     |
| ------------------------------------------ | ------------ | --------------------------------------------------------- |
| 1.32.322                                   | 6/12/2026    | Add support for the remaining Aembit Credential Providers |
| 1.31.314                                   | 5/15/2026    | Graduate CRDs from aembit.io/v1beta1 to aembit.io/v1      |
| 1.31.298                                   | 5/12/2026    | Initial release                                           |

### Lambda Extension

[Section titled “Lambda Extension”](#lambda-extension)

| Lambda Extension Version | Release Date | Notes                                                                                             |
| ------------------------ | ------------ | ------------------------------------------------------------------------------------------------- |
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
| Show 21 more versions    |              |                                                                                                   |

### Lambda Layer

[Section titled “Lambda Layer”](#lambda-layer)

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

### Virtual appliance

[Section titled “Virtual appliance”](#virtual-appliance)

| Virtual appliance Version | Release Date |
| ------------------------- | ------------ |
| 1.18.64                   | 11/14/2024   |
