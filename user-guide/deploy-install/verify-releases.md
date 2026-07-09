---
type: how-to
title: "Verifying Aembit binary release signatures"
description: "How to verify official Aembit binary release signatures"
resource: https://docs.aembit.io/user-guide/deploy-install/verify-releases/
interface: web-ui
tags: [deploy-install]
timestamp: 2026-06-26T13:11:59-07:00
type_inferred: true
---

# Verifying Aembit binary release signatures

Aembit cryptographically signs all binary releases which enables you to cryptographically verify the authenticity of those releases.

To verify binary release signatures, Aembit suggests using `gpg` and `shasum` to verify GPG signatures and file integrity.

## Prerequisites

[Section titled “Prerequisites”](#prerequisites)

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

[Section titled “Available releases”](#available-releases)

Here’s a list of all available Aembit binary releases:

* [Aembit Agent](https://releases.aembit.io/agent/index.html)
* [Agent Controller](https://releases.aembit.io/agent_controller/index.html)
* [Agent Proxy](https://releases.aembit.io/agent_proxy/index.html)
* [Aembit Edge Virtual Appliance](https://releases.aembit.io/edge_virtual_appliance/index.html)

## Verify a release

[Section titled “Verify a release”](#verify-a-release)

The following example shows how to verify the release signature for Agent Proxy. Though, you can swap the release name and version to any of the other available releases.

To verify the Agent Proxy release, follow these steps using the `gpg` and `shasum` commands. Select the tab that matches your operating system and architecture:

* Linux - amd64

  1. Download the Agent Proxy release version from the [Agent Proxy Releases page](https://releases.aembit.io/agent_proxy/index.html) along with the matching checksum files.

     Alternatively, you can download these files using `curl`, swapping out the highlighted release version with the version you're verifying:

     ```shell
     curl -O https://releases.aembit.io/agent_proxy/1.32.4999/linux/amd64/aembit_agent_proxy_linux_amd64_1.32.4999.tar.gz
     curl -O https://releases.aembit.io/agent_proxy/1.32.4999/linux/amd64/aembit_agent_proxy_linux_amd64_1.32.4999.tar.gz.sha256
     curl -O https://releases.aembit.io/agent_proxy/1.32.4999/linux/amd64/aembit_agent_proxy_linux_amd64_1.32.4999.tar.gz.sha256.sig
     ```

  2. Import Aembit's public GPG key from [Keybase](https://keybase.io/aembit) into `gpg`:

     ```shell
     curl "https://keybase.io/aembit/pgp_keys.asc" | gpg --import
     ```

  3. Verify Agent Proxy's checksum integrity and authenticity with `gpg`:

     ```shell
     gpg --verify aembit_agent_proxy_linux_amd64_1.32.4999.tar.gz.sha256.sig aembit_agent_proxy_linux_amd64_1.32.4999.tar.gz.sha256
     ```

     *If you don't have `gpg` installed, see [Verifying Aembit binary release signatures prerequisites](verify-releases.md#prerequisites)*.

     Your output should look similar to the following and include the highlighted line:

     ```shell
     gpg --verify aembit_agent_proxy_linux_amd64_1.32.4999.tar.gz.sha256.sig aembit_agent_proxy_linux_amd64_1.32.4999.tar.gz.sha256
     gpg: Signature made Wed Sep 18 10:13:57 2024 PDT
     gpg:                using RSA key EA3D8D2FDAC6BD8137163D00D655E64729BC67D7
     gpg: Good signature from "Aembit, Inc. <keybase@aembit.io>" [unknown]
     ...
     ```

     As long as you see `Good signature...`, you know that the checksum files are valid and authentic.

     Warnings explained

     * **"\[unknown]"** means you haven't explicitly told GPG to trust this particular signing key.
     * **"WARNING: This key is not certified with a trusted signature!"** is GPG being cautious. GPG can verify the signature is cryptographically valid, but it doesn't know if you trust that this key actually belongs to Aembit.

  4. Verify the integrity of the Agent Proxy file you downloaded using `shasum`:

     ```shell
     shasum -a 256 aembit_agent_proxy_linux_amd64_1.32.4999.tar.gz.sha256
     ```

     If `shasum` returns a match, you know the file is intact and matches Aembit's original. The long hex string is the SHA256 hash that both your file and the checksums file agree on. No output would mean the checksums don't match.

* Windows - amd64

  1. Download the Agent Proxy release version from the [Agent Proxy Releases page](https://releases.aembit.io/agent_proxy/index.html) along with the matching checksum files.

     Alternatively, you can download these files using `curl`, swapping out the highlighted release version with the version you're verifying:

     ```shell
     curl -O https://releases.aembit.io/agent_proxy/1.32.4999/windows/amd64/aembit_agent_proxy_windows_amd64_1.32.4999.msi
     curl -O https://releases.aembit.io/agent_proxy/1.32.4999/windows/amd64/aembit_agent_proxy_windows_amd64_1.32.4999.msi.sha256
     curl -O https://releases.aembit.io/agent_proxy/1.32.4999/windows/amd64/aembit_agent_proxy_windows_amd64_1.32.4999.msi.sha256.sig
     ```

  2. Import Aembit's public GPG key from [Keybase](https://keybase.io/aembit) into `gpg`:

     ```shell
     curl "https://keybase.io/aembit/pgp_keys.asc" | gpg --import
     ```

  3. Verify Agent Proxy's checksum integrity and authenticity with `gpg`:

     ```shell
     gpg --verify aembit_agent_proxy_windows_amd64_1.32.4999.msi.sha256.sig aembit_agent_proxy_windows_amd64_1.32.4999.msi.sha256
     ```

     *If you don't have `gpg` installed, see [Verifying Aembit binary release signatures prerequisites](verify-releases.md#prerequisites)*.

     Your output should look similar to the following and include the highlighted line:

     ```shell
     gpg --verify aembit_agent_proxy_windows_amd64_1.32.4999.msi.sha256.sig aembit_agent_proxy_windows_amd64_1.32.4999.msi.sha256
     gpg: Signature made Wed Sep 18 10:13:57 2024 PDT
     gpg:                using RSA key EA3D8D2FDAC6BD8137163D00D655E64729BC67D7
     gpg: Good signature from "Aembit, Inc. <keybase@aembit.io>" [unknown]
     ...
     ```

     As long as you see `Good signature...`, you know that the checksum files are valid and authentic.

     Warnings explained

     * **"\[unknown]"** means you haven't explicitly told GPG to trust this particular signing key.
     * **"WARNING: This key is not certified with a trusted signature!"** is GPG being cautious. GPG can verify the signature is cryptographically valid, but it doesn't know if you trust that this key actually belongs to Aembit.

  4. Verify the integrity of the Agent Proxy file you downloaded using `shasum`:

     ```shell
     shasum -a 256 aembit_agent_proxy_windows_amd64_1.32.4999.msi.sha256
     ```

     If `shasum` returns a match, you know the file is intact and matches Aembit's original. The long hex string is the SHA256 hash that both your file and the checksums file agree on. No output would mean the checksums don't match.

## Verify specific releases

[Section titled “Verify specific releases”](#verify-specific-releases)

Use the commands from the following sections to verify specific releases. You can verify all releases with the same GPG key.

The commands should look similar to the following examples, where you swap out the highlighted version with the specific version that you want to verify.

### Aembit Agent

[Section titled “Aembit Agent”](#aembit-agent)

**Release**: `Aembit Agent 1.31.4764`

**Downloads**: [Aembit Agent Releases page](https://releases.aembit.io/agent/index.html)

**Verification commands**:

```shell
# Verify checksum integrity and authenticity
gpg --verify aembit_1.31.4764_SHA256SUMS.sig aembit_1.31.4764_SHA256SUMS


# Verify file integrity
grep $(shasum -a 256 aembit_1.31.4764_linux_x64.zip) aembit_1.31.4764_SHA256SUMS
```

*Swap highlighted version with your target version if different from latest.*

### Agent Controller

[Section titled “Agent Controller”](#agent-controller)

**Release**: `Agent Controller 1.32.3502`

**Downloads**: [Agent Controller Releases page](https://releases.aembit.io/agent_controller/index.html)

**Verification commands**:

```shell
# Verify checksum integrity and authenticity
gpg --verify aembit_agent_controller_linux_x64_1.32.3502.tar.gz.sha256.sig aembit_agent_controller_linux_x64_1.32.3502.tar.gz.sha256


# Verify file integrity
shasum -a 256 aembit_agent_controller_linux_x64_1.32.3502.tar.gz.sha256
```

*Swap highlighted version with your target version if different from latest.*

### Agent Proxy

[Section titled “Agent Proxy”](#agent-proxy)

**Release**: `Agent Proxy 1.32.4999`

**Downloads**: [Agent Proxy Releases page](https://releases.aembit.io/agent_proxy/index.html)

**Verification commands**:

```shell
# Verify checksum integrity and authenticity
gpg --verify aembit_agent_proxy_linux_x64_1.32.4999.tar.gz.sha256.sig aembit_agent_proxy_linux_x64_1.32.4999.tar.gz.sha256


# Verify file integrity
shasum -a 256 aembit_agent_proxy_linux_x64_1.32.4999.tar.gz.sha256
```

*Swap highlighted version with your target version if different from latest.*

### Aembit Edge Virtual Appliance

[Section titled “Aembit Edge Virtual Appliance”](#aembit-edge-virtual-appliance)

**Release**: `Aembit Edge Virtual Appliance 1.18.64`

**Downloads**: [Aembit Edge Virtual Appliance Releases page](https://releases.aembit.io/virtual_appliance/index.html)

**Verification commands**:

```shell
# Verify checksum integrity and authenticity
gpg --verify aembit_edge_virtual_appliance_1.18.64.ova.sha256.sig aembit_edge_virtual_appliance_1.18.64.ova.sha256


# Verify file integrity
shasum -a 256 aembit_edge_virtual_appliance_1.18.64.ova.sha256
```

*Swap highlighted version with your target version if different from latest.*
