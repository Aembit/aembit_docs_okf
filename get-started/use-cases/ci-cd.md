---
type: explanation
title: "Securing CI/CD pipelines"
description: "How Aembit secures workload access in CI/CD environments"
resource: https://docs.aembit.io/get-started/use-cases/ci-cd/
tags: ["use-case"]
timestamp: 2026-04-13T13:05:24-07:00
---

# Securing CI/CD pipelines

Most teams running CI/CD pipelines manage secrets in environment variables, vaults, or hardcoded in scripts. Rotating these credentials is tedious and risky. Every rotation requires updating pipeline configurations, risking outages, and burning engineering hours. For regulated industries, auditability is critical: who accessed what, when, and why? CI/CD pipelines are particularly attractive targets because they often hold the highest privileges in the deployment chain. Static secrets stored in CI/CD systems also create attack vectors that many teams underestimate.

## Risks in CI/CD secret management

Keeping secrets out of code isn’t enough to protect them. Common CI/CD patterns leave credentials exposed in ways that teams often overlook.

Compromised pipeline credentials with high privileges

Teams often configure AWS administrator credentials, database root passwords, or API keys with full account access directly into CI/CD secret stores. A single compromised pipeline job (through dependency confusion, supply chain attacks, or malicious PR merges) can exfiltrate these credentials. For enterprise environments, this could enable multi-million dollar cloud resource abuse or data exfiltration. Consider a scenario where new development teams create AWS accounts and store administrator credentials in pipeline settings. A compromise could allow unauthorized resource provisioning, cryptomining operations, or sensitive data theft, creating both financial and regulatory exposure.

Secrets leaked through pipeline logs

Even with log masking showing asterisks instead of actual secrets, a compromised developer account can merge code that exfiltrates credentials via network requests. For example, malicious code could send environment variables to an external server in a legitimate-looking HTTP request. The secret never appears in logs, but it’s stolen anyway. Short-lived credentials limit the damage: even if exfiltrated, they expire before an attacker can exploit them.

Pipeline configurations committed to repos

Many teams version-control their GitLab CI YAML files or GitHub Actions workflow files. If these files reference secrets directly, even as variable names, and if those secrets are later exposed through environment dumps or logs, attackers can correlate variable names with actual values. Worse, if the configuration includes base64-encoded secrets for convenience, those are immediately compromised once someone clones the repo.

## What Aembit solves

Aembit assigns a unique identity to each CI/CD job using [Trust Providers](../concepts/trust-providers.md), which verify that the pipeline job is authentic. Instead of storing static secrets in your pipeline configuration, jobs request [credentials dynamically](../concepts/credential-providers.md) from Aembit at runtime. Aembit issues short-lived tokens or credentials based on [Access Policies](../concepts/access-policies.md) you define.

* Aembit removes static secrets from pipeline configurations, runners, and repository files, eliminating exposure through logs, version control, or compromised pipeline jobs.
* Aembit handles credential rotation centrally, with no pipeline downtime.
* Aembit logs every credential request for straightforward audits.
* Aembit secures access in both directions: your pipelines accessing external systems and external systems accessing your CI/CD platform APIs.

Example: External monitoring tools accessing GitHub APIs

Your security scanning tool needs to query GitHub for repository activity, open pull requests, and workflow run results. Instead of creating a long-lived Personal Access Token for the scanning tool, Aembit provisions short-lived tokens on-demand. If an attacker compromises the scanning tool, the blast radius covers only the current token’s lifetime, not months or years.

## Reduce secrets management costs

Traditional secrets managers like AWS Secrets Manager and HashiCorp Vault charge per secret stored, per API call to retrieve that secret, and per rotation operation. For large organizations running hundreds or thousands of pipeline jobs daily, this adds up fast.

One large financial services company reported spending $500,000 annually on secrets management alone for their CI/CD infrastructure. Aembit issues credentials on-demand rather than storing thousands of static secrets, which reduces costs markedly while improving security. In this case, the organization projected reducing their secrets management spend to approximately $100,000 by eliminating per-secret storage costs and API call volume.

Why secrets-per-API pricing becomes expensive

Traditional secrets management charges per secret stored and per API call to retrieve that secret. In a CI/CD environment with hundreds of jobs running daily, each job reads multiple secrets like database credentials, API keys, and cloud provider tokens. The API call volume accumulates fast. Consider a financial services company with 50 teams each running 20 pipeline jobs per day, averaging 5 secret reads per job. That generates 5,000 secret retrievals daily—about 2 million per year. At typical pricing tiers, this becomes a substantial line item. Aembit eliminates per-secret storage costs by issuing credentials dynamically rather than storing them persistently.

## Real example: GitLab service account management

Suppose your team manages multiple CI/CD pipelines that need access to GitLab’s API. Each pipeline stores Personal Access Tokens (PATs) in your pipeline configuration or environment variables. Rotating these tokens is manual and error-prone, and auditing who accessed what’s difficult.

With Aembit, the workflow looks like this:

![CI/CD pipeline job authenticating through Aembit to receive a short-lived GitLab Personal Access Token for API access](https://docs.aembit.io/d2/docs/get-started/use-cases/ci-cd-0.svg)

When a GitLab CI pipeline job starts, Aembit authenticates it using the [Gitlab Trust Provider](../../user-guide/access-policies/trust-providers/gitlab-trust-provider.md). This verifies the job is legitimate before proceeding.

Once authenticated, the job requests credentials from Aembit. Aembit checks your [Access Policies](../concepts/access-policies.md) to determine if this job can access GitLab. If approved, Aembit retrieves or provisions a Personal Access Token (PAT) through the [Managed GitLab Account Credential Provider](../../user-guide/access-policies/credential-providers/managed-gitlab-account.md).

Aembit injects this short-lived token into the job at runtime. The job uses it to call GitLab’s API, then the token expires automatically.

Next time the job runs, the process repeats with a fresh token. No manual rotation needed, no static secrets stored in your repository, and Aembit logs every access for compliance. If you need to revoke access to a specific job, update the [Access Policy](../../user-guide/access-policies/overview.md), not dozens of stored credentials.

### Why this architecture matters for CI/CD

Unlike traditional secret management where static, long-lived tokens sit in pipeline configuration or environment variables, Aembit provisions short-lived credentials on-demand at job runtime.

If pipeline logs, artifacts, or repository history expose a token, its short lifespan limits the window of exposure and potential damage. Revoking access happens immediately through policy changes, with no need to rotate credentials across dozens of systems or runners. An employee departure or security incident triggers access revocation with a single policy update.

Aembit also logs every credential request and usage, so security and compliance teams can determine which workload accessed which system, and when.

## Beyond CI/CD: Platform engineering and automation orchestration

GitHub and GitLab increasingly serve as automation platforms for orchestrating activities across organizations, extending beyond traditional code commit, test, and deploy workflows. Platform engineering teams use these systems to manage configuration, provision resources, and coordinate activities across enterprise systems.

Example: Automating identity provider configuration

Some organizations use GitHub Actions or GitLab CI as an orchestration layer to manage configuration across enterprise systems. For example, a financial services company might use GitHub workflows to automate Okta user provisioning, group assignments, and policy updates whenever organizational changes occur. Traditional approaches require storing long-lived Okta API tokens in GitHub secrets, which creates a significant security risk if an attacker gains access to those secrets. Aembit eliminates this risk by issuing short-lived credentials dynamically to each workflow run.

Aembit secures credentials for both traditional CI/CD pipelines and these broader automation orchestration use cases. Whether your GitHub Actions workflow deploys code or configures your identity provider, the same architecture applies: verify job identity, check policy, issue short-lived credentials, and log everything.

## Supported platforms

Aembit provides native integrations with major CI/CD platforms. Each integration uses your platform’s built-in identity capabilities for secure workload authentication:

* **GitLab** (including GitLab Dedicated and Self-Managed) - Uses GitLab CI tokens for authentication
* **GitHub** (via OIDC tokens) - Leverages GitHub’s native OIDC provider

Additionally, Aembit supports CI/CD platforms through OIDC ID Token Trust Providers, including Jenkins and other systems that support OpenID Connect.

## Next steps

**Choose your platform:**

* [GitLab CI/CD](../../user-guide/deploy-install/ci-cd/gitlab/overview.md) - Deploy Aembit Edge or use the GitLab CI/CD Component
* [GitHub Actions](../../user-guide/deploy-install/ci-cd/github/overview.md) - Deploy Aembit Edge with GitHub Actions
* [Jenkins Pipelines](../../user-guide/deploy-install/ci-cd/jenkins-pipelines.md) - Inject credentials into Jenkins using OIDC

**Next, configure your platform’s Trust Provider:**

* [GitLab Trust Provider](../../user-guide/access-policies/trust-providers/gitlab-trust-provider.md) - Authenticate GitLab CI jobs
* [GitHub Trust Provider](../../user-guide/access-policies/trust-providers/github-trust-provider.md) - Authenticate GitHub Actions jobs
* [OIDC ID Token Trust Provider](../../user-guide/access-policies/trust-providers/oidc-id-token-trust-provider.md) - For Jenkins and other OIDC-compatible platforms

**Explore related use cases:**

* [Multicloud Access](multicloud.md) - For pipelines deploying across AWS, Azure, and GCP
* [Database Access](database-access.md) - For securing database connections from your applications
* [Third-Party SaaS Access](third-party-access.md) - For pipelines that push to services like Datadog, PagerDuty, and Slack
