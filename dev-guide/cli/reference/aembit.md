---
type: reference
title: "aembit"
description: "Aembit CLI command reference"
resource: https://docs.aembit.io/dev-guide/cli/reference/aembit/
interface: cli
tags: ["reference", "cli"]
timestamp: 2026-09-08T23:32:41-07:00
---

# aembit

Base command for the Aembit CLI, which allows you to work with Aembit-managed credentials.

## Core commands

* [`aembit credentials get`](credentials-get.md) - retrieve credentials for a specific Client Workload

## Options

### `-h | --help`

Print help for the `aembit` command or the given subcommands.

### `-V | --version`

Print the version of the Aembit CLI.

## Examples

```shell
# Print the version of the Aembit CLI
aembit --version
Aembit Agent CLI 1.24.3328
```



```shell
# Print the help text for the Aembit CLI
aembit --help
Usage: aembit [OPTIONS] [COMMAND]


...
...
```
