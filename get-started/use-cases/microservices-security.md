---
type: explanation
title: "Securing microservices"
description: "How Aembit secures workload access between microservices"
resource: https://docs.aembit.io/get-started/use-cases/microservices-security/
tags: [use-case]
timestamp: 2026-04-13T13:05:24-07:00
---

# Securing microservices

In microservices setups (Kubernetes, Elastic Container Service (ECS), VMs), services need to authenticate to each other. Teams often rely on shared secrets, static API keys, or network controls. This creates lateral movement**Lateral movement**: An attack technique where an adversary moves through a network after initial compromise, using stolen credentials or exploited trust relationships to access additional systems and escalate privileges. risks, stale credentials, and limited visibility into service-to-service communication.

## What Aembit solves

[Section titled “What Aembit solves”](#what-aembit-solves)

Aembit issues unique, verifiable identities to each microservice using [Trust Providers](../concepts/trust-providers.md). When Service A calls Service B, Aembit issues a signed authentication token. This can be an [OpenID Connect (OIDC) ID Token](../../user-guide/access-policies/credential-providers/oidc-id-token.md). Alternatively, it can issue a [JWT-SVID Token](../../user-guide/access-policies/credential-providers/spiffe-jwt-svid.md). JWT-SVID stands for Secure Production Identity Framework For Everyone (SPIFFE) JSON Web Token. Service B validates this token using standard cryptographic libraries. Your [Access Policies](../concepts/access-policies.md) control which services can communicate with each other, and Aembit logs every interaction for audit visibility.

With this approach, you can:

* Remove shared secrets and static API keys from your microservices configurations
* Use SPIFFE JWT-SVID for service mesh**Service mesh**: A dedicated infrastructure layer that manages service-to-service communication in a microservices architecture, typically through sidecar proxies. Handles concerns like load balancing, encryption, authentication, and observability.[Learn more(opens in new tab)](https://glossary.cncf.io/service-mesh/) environments (for example, Istio, Consul, Kuma)
* Use OIDC**OpenID Connect (OIDC)**: An identity layer built on top of OAuth 2.0 that lets applications verify the identity of a user or workload and obtain basic profile information using JSON Web Tokens (JWTs).[Learn more(opens in new tab)](https://openid.net/developers/how-connect-works/) tokens for custom or legacy services that support standard JWT validation

Choosing between JWT-SVID and OIDC tokens

Aembit signs both JWT-SVID and OIDC tokens as JWTs that prove workload identity. The difference is the ecosystem they target.

**JWT-SVID** follows the SPIFFE specification. Service meshes like Istio, Consul, and Kuma natively understand SPIFFE identities and can validate JWT-SVIDs without custom configuration. If you run a service mesh, JWT-SVID is the natural fit because your Envoy sidecars already know how to verify them.

**OIDC ID Tokens** follow the OpenID Connect standard. Services that already validate JWTs using standard libraries (most web frameworks include this) can verify OIDC tokens without any SPIFFE awareness. If your services don’t run in a service mesh or you’re integrating with legacy services, OIDC tokens require less infrastructure.

You can use both in the same environment. Use JWT-SVID for service mesh traffic and OIDC tokens for services outside the mesh.

## Real example: SPIFFE JWT-SVID in service mesh

[Section titled “Real example: SPIFFE JWT-SVID in service mesh”](#real-example-spiffe-jwt-svid-in-service-mesh)

Consider an Istio-on-Amazon-EKS environment where microservices need to authenticate with each other. Without Aembit, you would typically run separate SPIRE**SPIRE**: Secure Production Identity Runtime Environment — the reference implementation of the SPIFFE specification. SPIRE issues and manages cryptographic identities for workloads in distributed systems.[Learn more(opens in new tab)](https://spiffe.io/docs/latest/spire-about/) (Secure Production Identity Runtime Environment) infrastructure just to issue certificates to your services. That adds another cluster component to manage, patch, and monitor.

What running separate SPIRE infrastructure involves

SPIRE requires deploying and operating its own server and agent components in your cluster. The SPIRE Server manages identity registration and signs SVIDs. SPIRE Agents run as DaemonSets on every node, performing workload attestation and delivering SVIDs to pods.

This means a separate certificate authority to secure, a separate control plane to monitor for availability, and a separate upgrade cycle to track against Kubernetes versions. If the SPIRE Server goes down, services can’t get new SVIDs and authentication fails.

Aembit replaces this infrastructure with a managed service. Identity attestation, credential issuance, and policy evaluation happen through Aembit’s control plane, so you don’t run or maintain SPIRE components in your cluster.

Aembit replaces this separate infrastructure:

![Kubernetes microservice authentication flow with Aembit sidecar requesting JWT-SVID tokens for service-to-service communication through Envoy proxy](https://docs.aembit.io/d2/docs/get-started/use-cases/microservices-security-0.svg)

When a microservice pod starts in your Kubernetes cluster, Aembit’s [Trust Provider](../concepts/trust-providers.md) authenticates it. The Trust Provider uses the pod’s Kubernetes ServiceAccount to verify identity. The pod registers its identity with Aembit.

When the service needs to call another service, it requests a credential from Aembit. Aembit issues a [JWT-SVID Token](../../user-guide/access-policies/credential-providers/spiffe-jwt-svid.md), a signed token in the SPIFFE standard format. Your [Access Policies](../concepts/access-policies.md) determine whether to grant the credential.

The service makes its request with this token in the Authorization header. The Istio Envoy proxies validate the token using SPIFFE standard verification. The request succeeds because Aembit confirmed the caller’s identity and verified that it has permission to access the target service.

You deploy Aembit as a sidecar in your [Kubernetes cluster](../../user-guide/deploy-install/kubernetes/overview.md), using the same deployment patterns as your other services. Compared to running separate SPIRE infrastructure, this reduces operational overhead while aligning with Zero Trust security practices. Aembit also gives you audit logs of every service-to-service interaction.

### Why this matters for microservices

[Section titled “Why this matters for microservices”](#why-this-matters-for-microservices)

Shared secrets and static API keys between services create lateral movement risk. If an attacker compromises one service, those credentials give access to every service that shares them.

With per-workload identity, each service authenticates individually. Compromising Service A doesn’t grant access to Service B because there are no shared credentials to steal. Access Policies define exactly which services can communicate, and Aembit logs every interaction for audit visibility.

## Supported platforms

[Section titled “Supported platforms”](#supported-platforms)

* Kubernetes (EKS, Azure Kubernetes Service (AKS), Google Kubernetes Engine (GKE))
* Istio, Consul, Kuma (via SPIFFE JWT-SVID)
* Any service supporting OIDC or JWT validation

## Next steps

[Section titled “Next steps”](#next-steps)

* [Deploy Aembit in Kubernetes](../../user-guide/deploy-install/kubernetes/overview.md) to get started with service-to-service identity
* [Configure JWT-SVID Credential Providers](../../user-guide/access-policies/credential-providers/spiffe-jwt-svid.md) for SPIFFE-based service mesh environments
* [Configure OIDC ID Token Credential Providers](../../user-guide/access-policies/credential-providers/oidc-id-token.md) for services that validate standard JWTs

**Related use cases:**

* For securing database connections from your microservices, see [Database Access](database-access.md)
* For microservices spanning multiple cloud providers, see [Multicloud Environments](multicloud.md)
* For microservices accessing third-party SaaS APIs, see [Third-Party Access](third-party-access.md)
