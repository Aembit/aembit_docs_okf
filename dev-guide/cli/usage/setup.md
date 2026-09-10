---
type: how-to
title: "Set up the Aembit CLI"
description: "A guide to installing Aembit CLI"
resource: https://docs.aembit.io/dev-guide/cli/usage/setup/
interface: cli
tags: ["usage", "cli"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Set up the Aembit CLI

## Prerequisites

Before setting up the Aembit CLI, ensure you have the following:

* A Linux or Windows Server system (see [Supported operating systems](../overview.md#supported-operating-systems))
* Access to a terminal or command prompt
* Internet access to download the CLI binary

## Choose a Linux build

Aembit publishes two Linux amd64 archives for each Aembit CLI version, and one Linux arm64 archive. Both amd64 archives extract to the same `aembit` executable, with the same commands, options, output, and configuration, and `aembit --version` prints the same string for both. They differ only in the C library the executable links against.

| Property      | Default build (musl)                                      | glibc build                                             |
| ------------- | --------------------------------------------------------- | ------------------------------------------------------- |
| Archive name  | `aembit_agent_cli_linux_amd64_<version>.tar.gz`           | `aembit_agent_cli_linux_amd64_glibc_<version>.tar.gz`   |
| Linking       | Static, with no dependency on the host C library          | Dynamic, which requires glibc 2.28 or newer on the host |
| Architectures | amd64 and arm64                                           | amd64 only                                              |
| Download path | `https://releases.aembit.io/agent/<version>/linux/amd64/` | The same directory as the default build                 |

Download the default build unless Aembit CLI fails to resolve your Aembit Tenant’s hostname with the error `failed to lookup address information` on a host where `dig` and `curl` resolve the same name. That failure comes from a DNS resolver response that musl rejects and glibc tolerates, so the glibc build resolves the name. For the full symptom and the resolver-side fix, see [DNS lookup fails with `failed to lookup address information`](../troubleshooting.md#dns-lookup-fails-with-failed-to-lookup-address-information).

The glibc build has no arm64 archive, and it doesn’t run on Alpine Linux or in distroless images, because those environments lack glibc. Each archive ships with a `.sha256` checksum file and a `.sha256.sig` detached signature file in the same directory.

## Download and setup Aembit CLI

To setup the Aembit Agent CLI, follow these steps:

* Linux

  1. Download the Aembit Agent CLI from:

     ```shell
     curl -O "https://releases.aembit.io/agent/1.34.5772/linux/amd64/aembit_agent_cli_linux_amd64_1.34.5772.tar.gz"
     ```

     To download the glibc build instead, use the archive name `aembit_agent_cli_linux_amd64_glibc_1.34.5772.tar.gz` in the same directory.

  2. Extract the CLI binary:

     ```shell
     tar -xf aembit_agent_cli_linux_amd64_1.34.5772.tar.gz
     ```

     The Aembit CLI is ready to use.

  3. Verify that you can run the Aembit CLI:

     ```shell
     ./aembit --version
     Aembit Agent CLI 1.34.5772
     ```

     > **Adding `aembit` to PATH**
     >
     > For convenience, you may want to add the `aembit` command to your `PATH` so you can run it from anywhere.
     >
     > You can do this by either:
     >
     > * Adding the following command to your `~/.profile`, which adds the current working directory where you extracted the `aembit` binary to your `PATH`:
     >
     >   ```shell
     >   export PATH="$PATH:$(pwd)"
     >   ```
     >
     > * Moving the `aembit` binary to a directory that’s already in your `PATH`, such as `/usr/local/bin`:
     >
     >   ```shell
     >   sudo mv aembit /usr/local/bin/
     >   ```

* Windows

  1. Download the Aembit Agent CLI from:

     ````powershell
     Invoke-WebRequest -Uri "https://releases.aembit-eng.com/agent/1.34.5772/windows/amd64/aembit_agent_cli_windows_amd64_1.34.5772.zip" -Outfile aembit_agent_cli_windows_amd64_1.34.5772.zip       ```
     ````

  2. Extract the CLI binary:

     ```powershell
     Expand-Archive -Path "aembit_agent_cli_windows_amd64_1.34.5772.zip" -DestinationPath "."
     ```

     The Aembit CLI is ready to use.

  3. Verify that you can run the Aembit CLI:

     ```powershell
     .\aembit.exe --version
     Aembit Agent CLI 1.34.5772
     ```

     > **Adding `aembit` to PATH**
     >
     > For convenience, you may want to add the `aembit` command to your `PATH` so you can run it from anywhere.
     >
     > You can do this by either:
     >
     > * Adding the directory where you extracted the `aembit.exe` binary to your system’s `PATH` environment variable.
     >
     > * Moving the `aembit.exe` binary to a directory that’s already in your `PATH`, such as `C:\Program Files\Aembit\`.
