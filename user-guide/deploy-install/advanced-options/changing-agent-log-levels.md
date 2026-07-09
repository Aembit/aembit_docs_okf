---
type: how-to
title: "How to change Edge Component log levels"
description: "How to change the log levels of Aembit's Edge Components"
resource: https://docs.aembit.io/user-guide/deploy-install/advanced-options/changing-agent-log-levels/
interface: web-ui
tags: [advanced-option, deploy-install]
timestamp: 2026-06-18T13:15:52-04:00
type_inferred: true
---

# How to change Edge Component log levels

Sometimes, you’ll want to use a different value than an Agent Controller’s or Agent Proxy’s default value for logging. For example, when troubleshooting a problem with your agent or when trying out a new feature.

The following sections detail how to change the log level of your:

* [Agent Controller](#change-agent-controller-log-level)

* [Agent Proxy](#change-agent-proxy-log-level)

Note

The process to change your Agent Controller’s or Agent Proxy’s log level does differ depending on your chosen deployment type. Make sure to use the correct tab in the sections to change your log levels.

See [Log level reference](../../../reference/edge-components/agent-log-level-reference.md) for complete details about each agent’s log levels.

## Change Agent Controller log level

[Section titled “Change Agent Controller log level”](#change-agent-controller-log-level)

Use the following tabs to set change your Agent Controller’s log level using the `AEMBIT_LOG_LEVEL` environment variable:

* Virtual Machine

  1. Log into your Agent Controller.

  2. Open the Aembit Agent Controller service at `/etc/systemd/system/aembit_agent_controller.service`.

     You may have to open this as root using `sudo`.

  3. Under `[Service]`, update or add `Environment=AEMBIT_LOG_LEVEL=<log_level>`, and set the log level you want. For example:

     /etc/systemd/system/aembit\_agent\_controller.service

     ```shell
     [Service]
     ...
     User=aembit_agent_controller
     Restart=always
     Environment=AEMBIT_TENANT_ID=abc123
     Environment=AEMBIT_DEVICE_CODE=
     Environment=AEMBIT_AGENT_CONTROLLER_ID=A12345
     Environment=ASPNETCORE_URLS=http://+:5000,http://+:9090
     Environment=AEMBIT_LOG_LEVEL=<log_level>
     StandardOutput=journal
     StandardError=journal
     ...
     ```

  4. Reload the Aembit Agent Controller config:

     ```shell
     systemctl daemon-reload
     ```

  5. Restart the Aembit Agent Controller service:

     ```shell
     systemctl restart aembit_agent_controller.service
     ```

* Windows

  On Windows, you set the log level by passing `AEMBIT_LOG_LEVEL` as an MSI property when you install or upgrade Agent Controller.

  Note

  Changing the log level requires reinstalling or upgrading Agent Controller because `AEMBIT_LOG_LEVEL` is an MSI property set at install time, not a runtime configuration.

  1. Run `msiexec` to install or upgrade Agent Controller with `AEMBIT_LOG_LEVEL` set to your desired log level:

     ```shell
     msiexec /i <agent_controller_msi> /l*v install.log `
       AEMBIT_TENANT_ID=<TENANT_ID> `
       AEMBIT_AGENT_CONTROLLER_ID=<CONTROLLER_ID> `
       AEMBIT_STACK_DOMAIN=<STACK_DOMAIN> `
       AEMBIT_LOG_LEVEL=<log_level>
     ```

  2. Verify the log level change by checking Agent Controller logs at:

     ```shell
     C:\ProgramData\Aembit\AgentController\Logs
     ```

## Change Agent Proxy log level

[Section titled “Change Agent Proxy log level”](#change-agent-proxy-log-level)

Use the following tabs to set change your Agent Proxy’s log level using the `AEMBIT_LOG_LEVEL` environment variable:

* Virtual Machine

  1. Log into your Agent Proxy.

  2. Open the Aembit Agent Proxy service at `/etc/systemd/system/aembit_agent_proxy.service`.

     You may have to open this as root using `sudo`.

  3. Under `[Service]`, update or add `Environment=AEMBIT_LOG_LEVEL=<log_level>`, and set the log level you want. For example:

     ```shell
     [Service]
     ...
     User=aembit_agent_proxy
     Restart=always
     StandardOutput=journal
     StandardError=journal
     TimeoutStopSec=20
     Nice=-20
     LimitNOFILE=65535
     Environment=AEMBIT_SIGTERM_STRATEGY=immediate
     Environment=AEMBIT_AGENT_CONTROLLER=https://my-proxy-service:5000
     Environment=AEMBIT_DOCKER_CONTAINER_CIDR=
     Environment=CLIENT_WORKLOAD_ID=
     Environment=AEMBIT_AGENT_PROXY_DEPLOYMENT_MODEL=vm
     Environment=AEMBIT_SERVICE_PORT=51234
     // highlight-next-line
     Environment=AEMBIT_LOG_LEVEL=<log_level>
     ...
     ```

  4. Reload the Aembit Agent Proxy config:

     ```shell
     systemctl daemon-reload
     ```

  5. Restart the Aembit Agent Proxy service:

     ```shell
     systemctl restart aembit_agent_proxy.service
     ```

* Windows

  On Windows, you set the log level by passing `AEMBIT_LOG_LEVEL` as an MSI property when you install or upgrade Agent Proxy.

  Note

  Changing the log level requires reinstalling or upgrading Agent Proxy because `AEMBIT_LOG_LEVEL` is an MSI property set at install time, not a runtime configuration.

  1. Run `msiexec` to install or upgrade Agent Proxy with `AEMBIT_LOG_LEVEL` set to your desired log level:

     ```shell
     msiexec /i <agent_proxy_msi> /l*v install.log `
       AEMBIT_AGENT_CONTROLLER=<AC_HOSTNAME>:5000 `
       AEMBIT_LOG_LEVEL=<log_level>
     ```

  2. Verify the log level change by checking Agent Proxy logs at:

     ```shell
     C:\ProgramData\Aembit\AgentProxy\Logs
     ```

     Tip

     The `ProgramData` folder is hidden by default. To access it, open File Explorer, select **View**, and enable **Hidden items**.
