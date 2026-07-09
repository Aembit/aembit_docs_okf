---
type: explanation
title: "Server Workloads"
description: "This document provides a high-level description of Server Workloads"
resource: https://docs.aembit.io/user-guide/access-policies/server-workloads/
tags: [server-workload, access-policy]
timestamp: 2026-02-26T15:41:06-08:00
type_inferred: true
---

# Server Workloads

Reuse this in another Resource Set

You can copy a Server Workload to another Resource Set. See [About component copying](../../administration/resource-sets/about-component-copying.md) to learn more.

## Using wildcard domains

[Section titled “Using wildcard domains”](#using-wildcard-domains)

In Aembit, wildcard domains simplify Server Workload configuration by allowing a single workload to handle requests across multiple services or regions. This is particularly useful for services with consistent domain structures like AWS’s `amazonaws.com`.

For example, using the wildcard domain `*.amazonaws.com` for [AWS Cloud](guides/aws-cloud.md) creates a reusable Server Workload that works across all AWS services and regions, eliminating the need to configure each one individually.

For more granular control, you can specify exact hostnames like `kms.us-east-1.amazonaws.com` to limit the Server Workload to a specific service and region.

## Server Workloads by category

[Section titled “Server Workloads by category”](#server-workloads-by-category)

The following sections break down the Server Workloads by category. Choose from the following pages to learn more about each category and its respective Server Workloads.

### AI and machine learning

[Section titled “AI and machine learning”](#ai-and-machine-learning)

* [Claude](guides/claude.md)
* [Gemini](guides/gemini.md)
* [OpenAI](guides/openai.md)

### CI/CD

[Section titled “CI/CD”](#cicd)

* [GitHub REST](guides/github-rest.md)
* [GitLab REST](guides/gitlab-rest.md)
* [SauceLabs](guides/saucelabs.md)

### Cloud platforms and services

[Section titled “Cloud platforms and services”](#cloud-platforms-and-services)

* [Apigee](guides/apigee.md)
* [AWS Cloud](guides/aws-cloud.md)
* [Microsoft Graph](guides/microsoft-graph.md)

### CRM

[Section titled “CRM”](#crm)

* [Salesforce REST](guides/salesforce-rest.md)

### Data analytics

[Section titled “Data analytics”](#data-analytics)

* [AWS Redshift](guides/aws-redshift.md)
* [Databricks](guides/databricks.md)
* [GCP BigQuery](guides/gcp-bigquery.md)
* [Looker Studio](guides/looker-studio.md)
* [Snowflake](guides/snowflake.md)

### Databases

[Section titled “Databases”](#databases)

For database protocol details and deployment requirements, see [Database protocol support](../../deploy-install/databases/overview.md).

* [AWS MySQL](guides/aws-mysql.md)
* [AWS PostgreSQL](guides/aws-postgres.md)
* [Local MySQL](guides/local-mysql.md)
* [Local PostgreSQL](guides/local-postgres.md)
* [Local Redis](guides/local-redis.md)
* [Oracle Database](guides/oracle-database.md)

### Financial services

[Section titled “Financial services”](#financial-services)

* [PayPal](guides/paypal.md)
* [Stripe](guides/stripe.md)

### IT tooling

[Section titled “IT tooling”](#it-tooling)

* [PagerDuty](guides/pagerduty.md)

### Productivity

[Section titled “Productivity”](#productivity)

* [Atlassian](guides/atlassian.md)
* [Box](guides/box.md)
* [Freshsales](guides/freshsales.md)
* [Google Drive](guides/google-drive.md)
* [Slack](guides/slack.md)

### Security

[Section titled “Security”](#security)

* [Aembit](guides/aembit.md)
* [Beyond Identity](guides/beyond-identity.md)
* [GitGuardian](guides/gitguardian.md)
* [HashiCorp Vault](guides/hashicorp-vault.md)
* [KMS](guides/kms.md)
* [Okta](guides/okta.md)
* [Snyk](guides/snyk.md)
