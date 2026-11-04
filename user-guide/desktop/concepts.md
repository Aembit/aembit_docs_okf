---
type: explanation
title: "Aembit Desktop concepts"
description: "How browser sign-in, the background services, and the local proxy combine to inject credentials."
resource: https://docs.aembit.io/user-guide/desktop/concepts/
tags: ["desktop"]
timestamp: 2026-11-04T14:15:50-08:00
---

# Aembit Desktop concepts

> **Aembit Desktop public beta**
>
> Aembit Desktop is in public beta. Commands and configuration may change before general availability.

Aembit Desktop authenticates a person in a browser, injects credentials into the traffic their tools send through a local proxy, and keeps the session alive for a period you set. For the steps, see [Set up user access](set-up-user-access.md) and [Install Aembit Desktop and sign in](install.md).

## What runs on the machine

Aembit Desktop installs one command, `adt`. `adt start` starts two background services that run as the signed-in person, and `adt status` reports on both:

* The **credential service** holds the person’s session tokens and renews them before they expire, without asking the person to sign in again.
* The **proxy service** listens on `127.0.0.1:8000` by default and handles the traffic that tools send to it.

The two services talk to each other, to Aembit Cloud, and to the Server Workloads your tools reach through the proxy. Neither listens on an address other than the loopback interface, and neither needs administrator privileges. Each runs with the privileges of the terminal whose `adt` command started it, which can be `adt login` as well as `adt start`.

The proxy service doesn’t authenticate the processes that connect to it, and it doesn’t identify which process or Windows account opened a connection. Any process on the machine that connects to the local proxy can reach every service an Access Policy allows the signed-in person, with the credential injected. Treat a signed-in session as access for everything that person runs on the machine.

## Signing in

`adt login` opens a browser, and the browser is where the person authenticates. Aembit Cloud redirects the browser to the Identity Provider your Aembit Tenant uses for Single Sign-On. The person signs in there under whatever rules you already enforce, and the Identity Provider returns them to Aembit Cloud.

Aembit Desktop holds no client secret, so the exchange uses the OpenID Connect (OIDC) authorization code flow with PKCE (Proof Key for Code Exchange). The session token that results carries standard OIDC claims, which is what an Access Policy matches on.

The authorization code comes back to a redirect URI on the machine’s own loopback interface. Exchanging it for session tokens requires the PKCE verifier that Aembit Desktop created for that sign-in.

![Aembit Desktop sign-in: the browser authenticates at the Identity Provider, Aembit Cloud returns an authorization code to the loopback redirect URI, and Aembit Desktop exchanges it for session tokens](https://docs.aembit.io/d2/docs/user-guide/desktop/concepts-0.svg)

The `--client-id` value the person passes to `adt login` is the **Aembit Client ID** shown on the **Users** tab of the Identity Provider in the Aembit Tenant UI. It identifies the Identity Provider that signs people in, not the person and not the machine, so every person who signs in through that Identity Provider uses the same value. A Client Workload’s Aembit Client ID is a different value, and `adt login` rejects it, along with any other client ID that isn’t for signing in a person.

## Injecting a credential

Once the person has signed in, a tool configured to use the local proxy sends its requests to the proxy service instead of to the service directly.

![Aembit Desktop credential injection: a tool sends a request to the local proxy, Aembit Cloud evaluates the Access Policy and returns the credential, and the proxy forwards the request with the credential injected](https://docs.aembit.io/d2/docs/user-guide/desktop/concepts-1.svg)

Aembit Cloud decides at step 2 by evaluating an Access Policy. For an HTTPS service, the proxy service makes that request once for each connection a tool opens. A tool that keeps the connection open reuses the result for later requests on it, until the connection sits idle for an hour. For a plain HTTP service, the proxy service makes the request for every request the tool sends.

When Aembit Cloud’s answer carries a lifetime, the proxy service keeps the answer for that long and doesn’t ask again until it expires. A change to an Access Policy, including one that removes access, reaches Aembit Desktop only after a kept answer expires.

The Client Workload in that policy identifies Aembit Desktop, the Trust Provider validates the identity the person signed in with, and the Credential Provider supplies the credential for the Server Workload the tool reaches. When no Client Workload matches the signed-in person, the proxy service passes an HTTPS connection through without reading it. The service then receives the request without an Aembit-brokered credential.

To read an HTTPS request and inject a credential into it, the proxy service terminates TLS and presents a certificate. Aembit Cloud issues it under your Aembit Tenant CA, or under the Client Workload’s [standalone certificate authority](../deploy-install/advanced-options/tls-decrypt/configure-tls-decrypt-standalone-ca.md) when it has one. The tool has to trust that CA, which is why the person retrieves the Aembit Tenant CA certificate before they route any traffic. Aembit Desktop writes the certificate to standard output or to a file and never installs it. Nothing changes the machine’s system trust store unless the person chooses to change it.

## How long a session lasts

Two limits apply to the tokens the credential service holds, and an Aembit Tenant administrator sets both on the Identity Provider:

* **Token Lifetime** is how long each access token lasts and how long a session can go without renewing, 24 hours by default. The minimum is 5 minutes.
* **Absolute Token Lifetime** is a hard limit on the session, 30 days by default. The session ends at that point whatever the person has been doing, and they sign in again. The maximum is 365 days, and the value has to be greater than the Token Lifetime.

The credential service renews the session on its own before the access token expires. Tool traffic doesn’t extend the session. The session ends early when the credential service goes a full Token Lifetime without renewing, for example because the person’s machine was off or `adt stop` ran.

A renewal can also fail, for example because the person’s machine had no network connection at that moment. The credential service then stops renewing, and tools stop working when the current access token expires. Run `adt stop` and then `adt start` to retry the renewal.

`adt logout` ends the session immediately and discards the stored tokens.

When the access token expires or the person runs `adt logout`, the proxy service has no session to act on, and it keeps running. When the person runs `adt login`, the credential service hands the new session to the proxy service, which uses it without a restart. The exception is a sign-in to a different Aembit Tenant while a session from the old one is still stored and the proxy service runs. `adt login` then asks to continue, signs the person out of the old Aembit Tenant, and restarts the proxy service. The restarted proxy service reads `HTTP_PROXY` and `HTTPS_PROXY` from the shell that ran `adt login`. Run that `adt login` from a shell where they don’t point at the local proxy, or the restart fails and the proxy service stays down.

## How settings combine

Aembit Desktop reads its settings from six layers, and a value in a higher layer wins over the same value in a lower one. From highest to lowest:

1. The managed settings file, `ManagedSettings.json`, which no other layer overrides.
2. An option on the `adt` command line.
3. An environment variable.
4. The person’s own settings file, `Settings.json`.
5. The system defaults file, `DefaultSettings.json`.
6. The built-in default.

An administrator places the two system files, and the person owns their own file. Aembit Desktop skips a file that doesn’t exist, and it ignores a key it doesn’t recognize, so a misspelled key has no effect and produces no error. A file that exists but isn’t valid JSON stops `adt start` with an error that names the file.

The background services read the settings when they start, so a change reaches them after `adt stop` and `adt start`. [Configure Aembit Desktop settings](configure.md) lists where each file lives and what it can set.

## What gets recorded

Aembit Cloud records an Access Authorization Event for every connection or request the proxy service asks it to authorize, whether it allows the request or not.

## Running alongside another proxy

Many organizations already route outbound traffic through a secure web gateway, and Aembit Desktop sits in front of one rather than replacing it. When `HTTP_PROXY` or `HTTPS_PROXY` names an upstream proxy at the moment `adt start` runs, the proxy service forwards traffic to it after injecting the credential. For a traffic type whose variable is unset, the proxy service uses the Windows manual proxy setting instead. It reads that setting again for every request, and it doesn’t read a proxy setup script or automatic proxy detection. [Running Edge Components behind TLS-inspecting proxies](../deploy-install/advanced-options/running-behind-secure-web-gateways.md) explains how such a gateway alters TLS traffic. Its steps apply to Agent Proxy and Agent Controller, not to Aembit Desktop.

Two things follow from that:

* **Start Aembit Desktop before you point the proxy variables at it.** The proxy service reads `HTTP_PROXY` and `HTTPS_PROXY` when it starts. Run `adt start` while they still name your organization’s gateway, then point them at the local proxy in the shell or the tool that needs them. When either variable already points at its own address, `adt start` reports `Unable to start the HTTP proxy`. For a traffic type whose variable is unset, `adt start` checks the Windows manual proxy setting instead. The error under the report names the traffic type and address and ends with `This would cause an infinite loop.`
* **Two certificate authorities are in play, on two different sides of the proxy.** Your tools trust your Aembit Tenant CA, or the Client Workload’s standalone certificate authority, because the local proxy presents a certificate issued under it. Aembit Desktop trusts the secure web gateway’s CA, because it’s the client that connects through the gateway.
