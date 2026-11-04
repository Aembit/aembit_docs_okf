---
type: explanation
title: "Aembit Desktop"
description: "Give people on Windows 11 machines access to enterprise HTTP and HTTPS services without static credentials."
resource: https://docs.aembit.io/user-guide/desktop/
tags: ["desktop"]
timestamp: 2026-11-04T14:15:50-08:00
---

# Aembit Desktop

> **Aembit Desktop public beta**
>
> Aembit Desktop is in public beta. Commands and configuration may change before general availability.

Aembit Desktop gives a person working on a Windows 11 machine access to enterprise HTTP and HTTPS services without holding static credentials. The person signs in once through your Identity Provider. The tools they run then reach those services through a proxy on their own machine, which injects credentials as each request passes through.

## The problem it solves

People and the AI agents they run locally need access to enterprise services, and today that access typically comes from an API key or a token copied onto the machine. Those credentials are long-lived, they sit in shell profiles and configuration files, and nothing records which person used one. Revoking a credential means finding every machine that holds a copy.

Aembit Desktop replaces the copied credential with a session. A person authenticates to your Identity Provider, and Aembit Cloud evaluates an Access Policy for each connection or request the person’s tools make. Aembit supplies the credential that reaches the service at the moment the request needs it. The machine keeps the person’s own session tokens, which [expire on a schedule you set](concepts.md#how-long-a-session-lasts). The local proxy adds the service credential to each request, so no tool on the machine holds it. Revoking access is a change to the Access Policy rather than a search of every machine. Aembit Desktop doesn’t identify which tool or agent on the machine made a request.

## How the pieces fit together

An **Aembit Tenant administrator** works in the Aembit Tenant UI. They turn on user access for an Identity Provider and build an Access Policy from four components:

* A Client Workload that identifies Aembit Desktop.
* A Trust Provider that validates the signed-in person.
* A Server Workload for the service people reach.
* A Credential Provider that supplies the credential.

A **person on a Windows 11 machine** installs Aembit Desktop and runs the `adt` command. They start the background services, sign in through a browser, and point their tools at the local proxy. From that point their tools reach the services the Access Policy allows, and neither the person nor the tool ever holds the service credential.

An administrator who rolls Aembit Desktop out to many machines can also deliver its settings, such as the Aembit Client ID people sign in with, as JSON files. A managed settings file overrides anything the person sets on their own machine. [Configure Aembit Desktop settings](configure.md) covers the files and the order they apply in.

## What it supports

Aembit Desktop supports:

* **Windows 11 on x64.** The public beta ships only a 64-bit Windows installer, and no other operating system has one.
* **HTTP and HTTPS traffic**, through the local proxy at `127.0.0.1`, on port 8000 unless you change it.
* **Sign-in through an OIDC 1.0 or SAML 2.0 Identity Provider you configure.** An Aembit Tenant that already uses an Identity Provider for Aembit Tenant UI Single Sign-On reuses that Identity Provider.
* **The default Resource Set only.** Aembit Desktop doesn’t support any other Resource Set, so create every component of an Aembit Desktop Access Policy in the default Resource Set.

Aembit Desktop doesn’t support:

* **Protocols other than HTTP and HTTPS.** Database, SSH, and other traffic doesn’t pass through the local proxy.
* **Transparent traffic interception.** Every tool that uses Aembit Desktop points at the local proxy explicitly, either through its own proxy setting or through the `HTTP_PROXY` and `HTTPS_PROXY` environment variables.
* **Applications that pin TLS certificates.** The local proxy terminates TLS with a certificate issued under your Aembit Tenant CA, or under the Client Workload’s standalone certificate authority, so a pinned application rejects the connection. Route these applications around the proxy.
* **Signing in without single sign-on.** Every person who uses Aembit Desktop signs in through your Identity Provider.
* **Starting after a restart.** Nothing starts Aembit Desktop when the machine restarts, so the person runs `adt start` again.
* **Telling local processes apart.** The local proxy doesn’t identify which process or Windows account opened a connection. Any process on the machine that connects to it reaches every service the signed-in person’s Access Policies allow.

> **Not enabled by default**
>
> Contact [Aembit support](https://aembit.io/contact) to enable this feature.

## In this section

* [Get started with Aembit Desktop](get-started.md) - Configure and test a complete path from a signed-in person to the GitHub REST API
* [Concepts](concepts.md) - How signing in, the background services, and the local proxy combine to inject credentials for a session
* [Set up user access](set-up-user-access.md) - Enable user access on your Identity Provider and build the Access Policy, in the Aembit Tenant UI
* [Install Aembit Desktop and sign in](install.md) - Install Aembit Desktop on a Windows 11 machine, sign in, and route a tool through the local proxy
* [Configure Aembit Desktop settings](configure.md) - Deliver settings to people’s machines through system, managed, and user settings files
* [Use the AWS CLI with Aembit Desktop](aws-cli.md) - Run AWS CLI commands with Aembit-brokered AWS credentials
* [Troubleshoot Aembit Desktop](troubleshooting.md) - Fix start, sign-in, settings, and certificate errors
* [Reference](reference.md) - The `adt` commands, options, settings, environment variables, defaults, and limits
