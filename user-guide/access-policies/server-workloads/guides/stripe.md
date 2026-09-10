---
type: how-to
title: "Stripe"
description: "This page describes how to configure Aembit to work with the Stripe Server Workload."
resource: https://docs.aembit.io/user-guide/access-policies/server-workloads/guides/stripe/
interface: web-ui
tags: ["financial", "guide", "server-workload", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Stripe


[Stripe](https://stripe.com/) is a digital payment processing service that allows businesses to accept and process payments online. Stripe supports various payment methods, including credit cards, and provides tools for managing subscriptions and recurring payments.

Below you can find the Aembit configuration required to work with the Stripe service as a Server Workload using the Stripe SDK or other HTTP-based client.

## Prerequisites

Before proceeding with the configuration, you will need to have a Stripe tenant (or [sign up](https://dashboard.stripe.com/register) for one).

## Server Workload Configuration

1. Create a new Server Workload.

* **Name** - Choose a user-friendly name.

2. Configure the service endpoint:

* **Host** - `api.stripe.com`
* **Application Protocol** - HTTP
* **Port** - 443 with TLS
* **Forward to Port** - 443 with TLS
* **Authentication method** - HTTP Authentication
* **Authentication scheme** - Bearer

## Credential Provider Configuration

1. Sign into your Stripe account.

2. Go to the Developers section.

3. Click on the API keys tab.

4. Ensure you are in the correct mode (Test mode for Stripe test data or Live mode for live production data).

![Create Stripe API token](https://docs.aembit.io/_astro/stripe_keys.n5lMth8U_wJVBT.webp)

5. You can either reveal and copy the Standard keys’ secret key or, for additional security, create and copy a Restricted key. Please read more about this in the [official Stripe documentation](https://stripe.com/docs/keys).

6. Create a new Credential Provider.

* **Name** - Choose a user-friendly name.
* **Credential Type** - [API Key](../../credential-providers/api-key.md)
* **API Key** - Provide the key copied from Stripe.

## Client Workload Configuration

Aembit now handles the credentials required to access the Server Workload, eliminating the need for you to manage them directly. You can safely remove any previously used credentials from the Client Workload.

If you access the Server Workload through an SDK or library, it is possible that the SDK/library may still require credentials to be present for initialization purposes. In this scenario, you can provide placeholder credentials. Aembit will overwrite these placeholder credentials with the appropriate ones during the access process.

## Access Policy

* Create an access policy for a Client Workload to access the Stripe Server Workload and assign the newly created Credential Provider to it.

## Required Features

* You will need to configure the [TLS Decrypt](../../../deploy-install/advanced-options/tls-decrypt/configure-tls-decrypt.md) feature to work with the Stripe Server Workload.

## Related

**Compatible credential providers**

* [API Key](../../credential-providers/api-key.md)
