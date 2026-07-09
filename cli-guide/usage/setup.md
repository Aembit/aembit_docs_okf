---
type: how-to
title: "Set up the Aembit CLI"
description: "A guide to installing Aembit CLI"
resource: https://docs.aembit.io/cli-guide/usage/setup/
interface: cli
tags: [usage]
timestamp: 2025-08-05T13:09:07-07:00
type_inferred: true
---

# Set up the Aembit CLI

## Prerequisites

[Section titled “Prerequisites”](#prerequisites)

Before setting up the Aembit CLI, ensure you have the following:

* A Linux or Windows Server system (see [Supported operating systems](../overview.md#supported-operating-systems))
* Access to a terminal or command prompt
* Internet access to download the CLI binary

## Download and setup Aembit CLI

[Section titled “Download and setup Aembit CLI”](#download-and-setup-aembit-cli)

To setup the Aembit Agent CLI, follow these steps:

* Linux

  1. Download the Aembit Agent CLI from:

     ```shell
     curl -O "https://releases.aembit.io/agent/1.24.3328/linux/amd64/aembit_agent_cli_linux_amd64_1.24.3328.tar.gz"
     ```

  2. Extract the CLI binary:

     ```shell
     tar -xf aembit_agent_cli_linux_amd64_1.24.3328.tar.gz
     ```

     The Aembit CLI is ready to use.

  3. Verify that you can run the Aembit CLI:

     ```shell
     ./aembit --version
     Aembit Agent CLI 1.24.3328
     ```

     Adding `aembit` to PATH

     For convenience, you may want to add the `aembit` command to your `PATH` so you can run it from anywhere.

     You can do this by either:

     * Adding the following command to your `~/.profile`, which adds the current working directory where you extracted the `aembit` binary to your `PATH`:

       ```shell
       export PATH="$PATH:$(pwd)"
       ```

     * Moving the `aembit` binary to a directory that’s already in your `PATH`, such as `/usr/local/bin`:

       ```shell
       sudo mv aembit /usr/local/bin/
       ```

* Windows

  1. Download the Aembit Agent CLI from:

     ````powershell
     Invoke-WebRequest -Uri "https://releases.aembit-eng.com/agent/1.24.3328/windows/amd64/aembit_agent_cli_windows_amd64_1.24.3328.zip" -Outfile aembit_agent_cli_windows_amd64_1.24.3328.zip       ```
     ````

  2. Extract the CLI binary:

     ```powershell
     Expand-Archive -Path "aembit_agent_cli_windows_amd64_1.24.3328.zip" -DestinationPath "."
     ```

     The Aembit CLI is ready to use.

  3. Verify that you can run the Aembit CLI:

     ```powershell
     .\aembit.exe --version
     Aembit Agent CLI 1.24.3328
     ```

     Adding `aembit` to PATH

     For convenience, you may want to add the `aembit` command to your `PATH` so you can run it from anywhere.

     You can do this by either:

     * Adding the directory where you extracted the `aembit.exe` binary to your system’s `PATH` environment variable.

     * Moving the `aembit.exe` binary to a directory that’s already in your `PATH`, such as `C:\Program Files\Aembit\`.
