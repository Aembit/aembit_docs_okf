---
type: explanation
title: "About Credential Providers"
description: "Understanding Credential Providers and their role in secure access credential management"
resource: https://docs.aembit.io/get-started/concepts/credential-providers/
tags: ["concept"]
timestamp: 2026-09-22T11:51:31-07:00
---

# About Credential Providers

Credential Providers bridge the gap between authorized Client Workloads and the authentication requirements of target Server Workloads. They obtain the specific access credentials—such as API keys, OAuth tokens, temporary cloud credentials, or signed tokens—that Client Workloads need to authenticate successfully to Server Workloads.

Credential Providers function as an abstraction layer, decoupling Client Workloads from the complex authentication mechanisms required by diverse Server Workloads. Whether a target service requires AWS federation, OAuth 2.0 flows, JWT validation, or basic API keys, the Client Workload doesn’t need to implement the corresponding protocol logic.

Aembit invokes Credential Providers only after rigorous security checks: first, Trust Providers verify the Client Workload’s identity through attestation, and second, all Access Conditions must pass. This ensures that credentials are only dispensed to trusted and authorized requesters.

![](https://docs.aembit.io/aembit-icons/gears-light.svg)

[Start configuring Credential Providers](../../user-guide/access-policies/credential-providers/overview.md)See Credential Providers in the User Guide

→

## How Credential Providers work

The following steps outline how Aembit uses Credential Providers during the authorization process:

1. **Request Access** - A Client Workload initiates a request to access a Server Workload, which Aembit Edge intercepts.

2. **Identity and Context Verification** - Aembit first verifies the workload’s identity through Trust Providers and evaluates all Access Conditions.

3. **Credential Provider Selection** - Once all security checks pass, Aembit selects the appropriate Credential Provider based on the matched Access Policy configuration.

4. **Backend Interaction** - The Credential Provider interacts with the relevant backend system (AWS Security Token Service (STS), OAuth server, internal vault, etc.) to obtain the required access credential.

5. **Credential Acquisition** - The provider generates, retrieves, or manages the specific credential format needed by the target Server Workload.

6. **Secure Transmission** - Aembit Cloud securely transmits the obtained credential back to the Aembit Edge component that intercepted the original request.

7. **Credential Injection** - Aembit Edge modifies the original client request by injecting the credential (typically into HTTP headers) before forwarding it to the Server Workload.

The following diagram illustrates this process:

![Credential provider process showing how Aembit obtains and injects credentials for client workloads](https://docs.aembit.io/d2/docs/get-started/concepts/credential-providers-0.svg)

## Supported provider types

Aembit offers multiple types of Credential Providers to accommodate the varied authentication mechanisms used by modern and legacy Server Workloads:

### Local providers

**Local Credential Providers** store and manage credential values within the Aembit platform itself. When invoked, Aembit retrieves the pre-configured secret from its internal secure storage.

**Supported local types:**

* **[API Key](../../user-guide/access-policies/credential-providers/api-key.md)** - For services authenticating via static API keys.
* **[Username & Password](../../user-guide/access-policies/credential-providers/username-password.md)** - For services using traditional username/password authentication.

**Common use cases:**

* Legacy systems that don’t support modern authentication methods
* Basic APIs requiring static key-based authentication
* Bridging authentication for systems during modernization transitions

### Remote providers

**Remote Credential Providers** interact with external systems to dynamically generate or retrieve access credentials on behalf of Client Workloads. Aembit acts as a broker to these external credential authorities.

**Cloud provider federations:**

* **[AWS Security Token Service Federation](../../user-guide/access-policies/credential-providers/aws-security-token-service-federation.md)** - Uses AWS Workload Identity Federation via OIDC to obtain temporary AWS credentials
* **[Azure Entra Workload Identity Federation](../../user-guide/access-policies/credential-providers/azure-entra-workload-identity-federation.md)** - Leverages OIDC federation to authenticate with Azure Entra ID
* **[Google Cloud Platform Workload Identity Federation](../../user-guide/access-policies/credential-providers/google-workload-identity-federation.md)** - Integrates with GCP WIF via OIDC for short-lived tokens

**Standards-based authentication:**

* **[JSON Web Token (JWT)](../../user-guide/access-policies/credential-providers/json-web-token.md)** - Generates and signs JWTs according to specified configurations
* **[OAuth 2.0 Authorization Code](../../user-guide/access-policies/credential-providers/oauth-authorization-code.md)** - Implements the full OAuth Authorization Code flow with user consent
* **[OAuth 2.0 Client Credentials](../../user-guide/access-policies/credential-providers/oauth-client-credentials.md)** - Uses Client Credentials flow for machine-to-machine authentication

**Platform-specific providers:**

* **[Aembit Access Token](../../user-guide/access-policies/credential-providers/aembit-access-token.md)** - Generates OIDC ID tokens for authenticating to the Aembit API itself
* **[Vault Client Token](../../user-guide/access-policies/credential-providers/vault-client-token.md)** - Authenticates to HashiCorp Vault via OIDC to retrieve Vault tokens
* **[Managed GitLab Account](../../user-guide/access-policies/credential-providers/managed-gitlab-account.md)** - Manages the credential lifecycle for GitLab service accounts

**Common use cases:**

* Accessing cloud services with temporary, scoped credentials
* Integrating with modern SaaS applications using OAuth 2.0
* Connecting to enterprise secrets management systems
* Authenticating to CI/CD platforms and development tools

### Advanced configurations

Aembit supports sophisticated configurations for complex scenarios:

* **[Multiple Credential Providers](../../user-guide/access-policies/credential-providers/multiple-credential-providers.md)** - Associate multiple providers with a single Access Policy for different authentication paths.
* **[Dynamic Claims](../../user-guide/access-policies/credential-providers/advanced-options/dynamic-claims.md)** - Customize token claims based on workload context.
* **[Vault Dynamic Claims](../../user-guide/access-policies/credential-providers/advanced-options/dynamic-claims-vault.md)** - Use dynamic claims to configure HashiCorp Vault roles based on workload attributes.
* **[Integration Options](../../user-guide/access-policies/credential-providers/integrations/overview.md)** - Extended integration capabilities for specialized platforms.

## Benefits of using Credential Providers

* **Security Abstraction** - Shields Client Workloads from complex authentication protocols, reducing the risk of implementation errors and credential exposure.

* **Dynamic Credential Management** - Facilitates the use of short-lived, ephemeral credentials wherever possible, reducing the risk of credential compromise.

* **Simplified Development** - Eliminates the need for developers to implement and maintain diverse authentication mechanisms in their applications.

* **Centralized Control** - Provides a single point of configuration and management for access credentials across heterogeneous environments.

* **Zero-Touch Authentication** - Enables “secretless” architectures where Client Workloads don’t need to handle credentials directly.

* **Policy-Driven Access** - Ensures credentials are only issued after identity verification and policy compliance, enforcing least privilege access.

* **Operational Flexibility** - Allows authentication method changes without modifying Client Workload code, supporting system modernization efforts.

* **Comprehensive Coverage** - Supports both modern federated authentication and legacy systems, enabling unified access management across diverse infrastructures.
