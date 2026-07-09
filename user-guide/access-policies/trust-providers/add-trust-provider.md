---
type: how-to
title: "How to add a Trust Provider"
description: "How to configure a Trust Provider for Client Workload identity attestation"
resource: https://docs.aembit.io/user-guide/access-policies/trust-providers/add-trust-provider/
interface: web-ui
tags: [trust-provider, access-policy]
timestamp: 2026-06-30T13:30:29-04:00
type_inferred: true
---

# How to add a Trust Provider

Trust Providers enable Aembit to authenticate without provisioning credentials or other secrets. Trust Providers are third-party systems or services that can attest identities with identity documents, tokens, or other cryptographically signed evidence.

Client Workload identity attestation is a core functionality to ensure only trusted Client Workloads can access the Server Workloads.

## Configure Trust Provider

[Section titled “Configure Trust Provider”](#configure-trust-provider)

If you are getting started with Aembit, configuring trust providers is optional; however, it’s critical to secure all production deployments.

1. Click the **Trust Providers** tab.

2. Click **+ New** to create a new Trust Provider.

3. Give the Trust Provider a name and optional description.

4. Choose the appropriate Trust Provider type based on your Client Workloads’ environment.

5. Follow the instructions for the Trust Provider based on your selection.

* [AWS Role Trust Provider](aws-role-trust-provider.md)
* [AWS Metadata Service Trust Provider](aws-metadata-service-trust-provider.md)
* [Azure Instance Metadata Service trust provider](azure-metadata-service-trust-provider.md)
* [Kerberos trust provider](kerberos-trust-provider.md)
* [Kubernetes Service account trust provider](kerberos-trust-provider.md)

6. Configure one or more **match rules** (specific to your Trust Provider type).

Note

Aembit recommends making matching criteria as tight as possible.

7. Click **Save**.

## Client Workload identity attestation

[Section titled “Client Workload identity attestation”](#client-workload-identity-attestation)

You must associate one or more Trust Providers with the existing Access Policy for Aembit to use Client Workload identity attestation.

1. Select an existing **Access Policy** to open the Access Policy Builder.

2. In the **Trust Provider** card in the right panel, click **+ Configure**.

3. Select the **Add New** tab to create a new Trust Provider, or select the **Select Existing** tab to choose from existing Trust Providers.

![Associate Trust Provider to Policy](https://docs.aembit.io/_astro/associate_trust_provider_to_policy.Cm0tLGtT_Z68kGk.webp)

## Agent Controller identity attestation

[Section titled “Agent Controller identity attestation”](#agent-controller-identity-attestation)

You must associate a Trust Provider with Agent Controller in order for Aembit to use Agent Controller for identity attestation.

1. Click the **Edge Components** tab.

2. Select one of the existing **Agent Controllers**.

3. Click **Edit**.

4. Choose from the dropdown one of the existing **Trust Providers**.

![Agent Controller Trust Provider Page](https://docs.aembit.io/_astro/agent_controller_trust_provider.B4GSihb0_1khLs4.webp)
