---
type: how-to
title: "Verifying Aembit binary release signatures"
description: "How to verify official Aembit binary release signatures"
resource: https://docs.aembit.io/user-guide/deploy-install/verify-releases/
interface: web-ui
tags: ["deploy-install"]
timestamp: 2026-09-09T21:06:51-07:00
---

# Verifying Aembit binary release signatures

Aembit cryptographically signs all binary releases which enables you to cryptographically verify the authenticity of those releases.

To verify binary release signatures, Aembit suggests using `gpg` and `shasum` to verify GPG signatures and file integrity.

## Prerequisites

Before verifying binary release signatures, you must:

* Have `gpg` (GNU Privacy Guard) installed.

  Expand to see steps to install `gpg`

  To install `gpg`, select a tab for your OS and follow the instructions:

  * Linux/WSL

    Debian:

    ```shell
    apt install gnupg
    ```

    RHEL:

    ```shell
    yum install gnupg2
    ```

  * MacOS

    ```shell
    brew install gnupg
    ```

  * Windows

    PowerShell:

    ```powershell
    winget install GnuPG.GnuPG
    ```

    Chocolatey:

    ```powershell
    choco install gpg4win
    ```

    Or, if you’re using WSL, follow the Linux/WSL tab’s instructions.

* Have `shasum` installed. `shasum` is pre-installed on most operating systems.

* Import Aembit’s public GPG key (you must have `gpg` installed for this command to work):

  ```shell
  curl "https://keybase.io/aembit/pgp_keys.asc" | gpg --import
  ```

## Available releases

* [Aembit CLI](https://docs.aembit.io/releases/agent/)
* [Agent Controller](https://docs.aembit.io/releases/agent-controller/)
* [Agent Proxy](https://docs.aembit.io/releases/agent-proxy/)
* [MCP Identity Gateway](https://docs.aembit.io/releases/mcp-gateway/)

Each release page lists every version, platform, and architecture, with the expected SHA-256 and copy-ready verification steps generated from the release itself. See [Prerequisites](#prerequisites) for the concepts, then visit the release page for the exact commands for your download.
