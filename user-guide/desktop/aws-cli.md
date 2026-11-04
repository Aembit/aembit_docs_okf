---
type: how-to
title: "Use the AWS CLI with Aembit Desktop"
description: "Run AWS CLI commands through Aembit Desktop with Aembit-brokered AWS credentials."
resource: https://docs.aembit.io/user-guide/desktop/aws-cli/
interface: cli
tags: ["desktop"]
timestamp: 2026-11-04T14:15:50-08:00
---

# Use the AWS CLI with Aembit Desktop

> **Aembit Desktop public beta**
>
> Aembit Desktop is in public beta. Commands and configuration may change before general availability.

Run AWS Command Line Interface (CLI) commands through Aembit Desktop without AWS access keys on your machine. `adt beta tool aws` runs `aws` with its requests routed through the local proxy, and Aembit Desktop signs each request with the AWS credential your Access Policy supplies.

## Before you begin

You need:

* Aembit Desktop installed and started, and a current sign-in. [Install Aembit Desktop and sign in](install.md) covers all three.

* The [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) installed and on your `PATH`.

* An active Access Policy that lets you reach AWS. Your Aembit Tenant administrator builds it by following [Set up user access](set-up-user-access.md), with a Server Workload and Credential Provider for AWS that [Create an AWS Server Workload](../access-policies/server-workloads/guides/aws-cloud.md) describes.

## Run an AWS CLI command

1. Put `adt beta tool aws` in place of `aws`, followed by the AWS CLI arguments you would normally type:

   ```powershell
   adt beta tool aws sts get-caller-identity
   ```

   `adt` sets `HTTPS_PROXY` to the local proxy and sets `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` to the placeholder `aembit-placeholder`, because the AWS CLI sends a request only when it holds keys. It also points `AWS_CA_BUNDLE` at a saved copy of your Aembit Tenant CA certificate. The AWS CLI then trusts the local proxy when the proxy presents a certificate issued under that certificate authority. Aembit Desktop replaces the placeholder signature with one made from the credential the Access Policy supplies.

2. Read the response.

   A successful response from AWS confirms that the request reached AWS with the Aembit-brokered credential, because the keys on your machine are placeholders.

`adt beta tool aws` doesn’t set an AWS Region. The AWS CLI reads the Region from your AWS configuration or from `AWS_REGION`, as it does without Aembit Desktop.

It exits with the exit code of the AWS CLI, so scripts can test the result as they would for `aws`. When `adt` can’t start `aws`, it prints `Unable to run the AWS CLI: Check that 'aws' is installed and in your PATH` and exits with `1`.

## Preview the environment

Add `--dry-run` before the AWS CLI arguments to print the environment variables and the command without running it:

```powershell
adt beta tool aws --dry-run s3 ls
```

Anything after the first AWS CLI argument passes through to `aws`, so `--dry-run` placed after it reaches the AWS CLI instead.

## Errors before the AWS CLI runs

`adt beta tool aws` checks your session before it starts `aws`:

* `Unable to reach the credential daemon: Start it using the command: adt start` means the background services aren’t running. Run `adt start`, then repeat the command.
* `Missing user credential: Log in using the command: adt login` means you haven’t signed in. Run `adt login`, then repeat the command.
* `Your user credential expired at <time>: Log in using the command: adt login` means your session ended. Run `adt login`, then repeat the command.
* `Unable to reach the proxy daemon: Start it using the command: adt start` or `The proxy daemon is not proxying HTTP.` means the proxy service isn’t running or isn’t listening. Run `adt stop` and then `adt start`, then repeat the command.
* `Unable to determine your Aembit tenant: Log in using the command: adt login` means Aembit Desktop can’t tell which Aembit Tenant your session belongs to. Run `adt login`, then repeat the command.

When the AWS CLI itself reports `CERTIFICATE_VERIFY_FAILED`, see [Troubleshoot Aembit Desktop](troubleshooting.md#the-aws-cli-reports-certificate_verify_failed).

## Limits

* `adt beta tool aws` sets `HTTPS_PROXY` but not `HTTP_PROXY` or `NO_PROXY`.
* `adt beta tool aws` passes every other variable in your shell to the AWS CLI unchanged, including other `AWS_` variables such as `AWS_SESSION_TOKEN`.
* `adt beta tool` runs the AWS CLI only.
