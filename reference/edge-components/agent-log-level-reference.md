---
type: reference
title: "Edge Component log levels"
description: "A reference page of all available Edge Component AEMBIT_LOG_LEVEL log levels"
resource: https://docs.aembit.io/reference/edge-components/agent-log-level-reference/
tags: ["edge-component"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Edge Component log levels

Aembit’s Edge Component’s such as Agent Controller and Agent Proxy have multiple log levels that you can set using the `AEMBIT_LOG_LEVEL` environment variable. Keep in mind that Agent Controller and Agent Proxy have slightly different values.

See the tables in the following sections for the available log levels and their descriptions:

* [Agent Controller](#agent-controller-log-levels)
* [Agent Proxy](#agent-proxy-log-levels)

To change your Agent Controller’s and Agent Proxy’s log levels, see [Changing log levels](../../user-guide/deploy-install/advanced-options/changing-agent-log-levels.md).

> **Tip**
>
> All log levels are **case-insensitive**, so Aembit treats `ERROR`, `Error`, and `error` the same.

## Agent Controller log levels

The following table contains the *Agent Controller* log levels and their descriptions for when setting the `AEMBIT_LOG_LEVEL` environment variable:

| Log level        | Description                                                                                                                                                                              |
| ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `fatal`          | System is unusable. Critical failures requiring immediate attention, often leading to Agent Controller shutdown.                                                                         |
| `error`          | Function-level failures that impact operations but don’t crash Agent Controller. These indicate significant problems that need attention but allow Agent Controller to continue running. |
| `warning` **\*** | Potentially harmful situations that don’t disrupt core functionality. These highlight issues that could become problems but aren’t blocking operations. \*Default value                  |
| `information`    | Normal operational messages highlighting key events. These track expected Agent Controller behavior and state changes.                                                                   |
| `debug`          | Detailed information useful during development. These messages expose internal Agent Controller state and control flow.                                                                  |
| `verbose`        | Most granular logging, showing all possible detail. These capture every minor operation and state change within Agent Controller.                                                        |

## Agent Proxy log levels

The following table contains the *Agent Proxy* log levels and their descriptions for when setting the `AEMBIT_LOG_LEVEL` environment variable:

| Log level     | Description                                                                                                                                                                            |
| ------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `error`       | Function-level failures that impact operations but don’t crash the Agent Proxy. These indicate significant problems that need attention but allow the Agent Proxy to continue running. |
| `warn`        | Potentially harmful situations that don’t disrupt core functionality. These highlight issues that could become problems but aren’t blocking operations.                                |
| `info` **\*** | Normal operational messages highlighting significant events in the Agent Proxy’s lifecycle. These track expected Agent Proxy behavior and state changes. \*Default value               |
| `debug`       | Detailed information useful during development and troubleshooting. These messages expose internal Agent Proxy state and control flow.                                                 |
| `trace`       | Most granular logging level showing step-by-step execution flow. These capture every minor operation and state change within the Agent Proxy.                                          |
| `off`         | Disables all logging output. Aembit records no messages regardless of their severity level.                                                                                            |
