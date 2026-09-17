# Aembit Changelog

## 2026-09-16

### GitHub Actions now selects among multiple AWS STS Credential Providers

The Aembit GitHub Action can now choose which AWS Security Token Service (STS) Federation Credential Provider to use when an Access Policy holds more than one.

An Access Policy can hold more than one AWS STS Federation Credential Provider, each with its own Access Key ID selector. Until now, a GitHub Actions workflow needed a self-hosted runner running Aembit Edge to reach more than one IAM role from a single Access Policy. The alternative was a direct call to the Edge API.

**Key capabilities:**

* The `aws-access-key-id` input names the Access Key ID selector of the Credential Provider you want, so a workflow on a GitHub-hosted runner reaches any IAM role in the Access Policy. See [Retrieve credentials in GitHub Actions](user-guide/deploy-install/ci-cd/github/github-actions-how-to.md).
* The Edge API credentials endpoint carries the same selector as connection metadata, alongside the selectors for multiple JSON Web Token (JWT) Credential Providers. See the [Edge API credentials endpoint](dev-guide/api/edge/endpoints/credentials.md).
* The Edge SDKs for TypeScript and Python pass the selector as connection metadata on the credential request. See [Select among multiple Credential Providers with the Edge SDK](dev-guide/sdk/edge/multiple-credential-providers.md).

**Minimum version required:**

* Aembit GitHub Action 1.3.0
* Edge SDK 1.34.0

For how Aembit matches a selector to a Credential Provider, see [Using multiple AWS STS Credential Providers](user-guide/access-policies/credential-providers/aws-security-token-service-multiple.md).

### Aembit Cloud configuration updates

Aembit Cloud corrects configuration workflows across Content Security, Client Workloads, Trust Providers, and authorization event reporting.

Key Updates:

* **Content Security tool lists**: Switching between **Allow** and **Block** in a [Content Security](user-guide/access-policies/content-security/overview.md) Provider keeps the tool list you configured, and **Remove Tools** clears the whole list in a separate step that asks you to confirm.
* **Redirect URI fields**: A [Redirect URI](user-guide/access-policies/client-workloads/identification/redirect-uri.md) field on a Client Workload accepts a pasted value in every position, inserts a long pasted value rather than overwriting the text around it, and shows one validation message at a time. Copying the full contents of a field leaves the browser responsive.
* **Trust Provider secrets in custom Resource Sets**: Terraform reads the state of a Trust Provider secret when that Trust Provider belongs to a custom [Resource Set](user-guide/administration/resource-sets/overview.md).
* **Authorization event timespan filters**: The date filters on [Access Authorization Events](user-guide/audit-report/access-authorization-events.md) offer a maximum timespan of 24 hours, the window over which the Aembit Tenant UI makes authorization events available.

## 2026-09-15

### MCP Identity Gateway 1.34.5794 release

Aembit has released [MCP Identity Gateway](user-guide/deploy-install/mcp-identity-gateway/overview.md) version 1.34.5794.

For the latest available versions of these components, see the [Edge Components Supported Versions](reference/edge-components/edge-component-supported-versions.md) page.

Key Updates:

* **MCP Tool Access Control enforcement**: The Gateway now enforces [MCP Tool Access Control](user-guide/access-policies/content-security/mcp-tool-access-control/overview.md), a Content Security Provider you add to an Access Policy. The Tool Visibility control decides which MCP tools an AI agent discovers, and the Tool Invocation control decides which MCP tools an AI agent can call.
* **Glob patterns for MCP tool names**: An entry in either control takes an exact MCP tool name or a glob pattern, and matching is case-sensitive. A pattern matches the name the MCP server publishes, not the prefixed name an AI agent displays. See [MCP Tool Access Control tool name reference](user-guide/access-policies/content-security/mcp-tool-access-control/reference.md).
* **Tool filtering decisions in your events**: [MCP workload events](user-guide/audit-report/workload-events/supported-protocols.md#content-security) record which control decided an MCP tool’s fate, which rules matched, which MCP tools a listing removed, and how many it returned, under `application.mcp.contentSecurity`.

A [security update](https://docs.aembit.io/changelog/entry/2026-09-15-mcp-identity-gateway-dependency-security-updates) also shipped in this build.

### MCP Identity Gateway security updates

MCP Identity Gateway version 1.34.5794 includes security updates.

For the latest available versions of these components, see the [Edge Components Supported Versions](reference/edge-components/edge-component-supported-versions.md) page.

## 2026-08-27

### Developer Guide combines the API, CLI, and SDK documentation

The [Developer Guide](dev-guide/overview.md) is now the single home for every way you integrate with Aembit from code, commands, or infrastructure. It replaces the separate API Guide and CLI Guide, which each covered one surface and left you to work out which one fit your workload. The new Edge SDK section lands inside it rather than beside it.

The guide opens by separating the two decisions an integration involves. First, choose how your workload gets a credential at runtime, through Agent Proxy, the Edge SDK, the Aembit CLI, or the Edge API. Second, choose how you manage the Aembit configuration behind that path, in the Aembit Tenant UI, through the Cloud API, or as Terraform code.

**What’s new:**

* **One guide for every developer surface**: [Aembit SDKs](dev-guide/sdk/edge/overview.md), [Aembit APIs](dev-guide/api/overview.md), [Aembit CLI](dev-guide/cli/overview.md), [Agent Proxy](dev-guide/integration/agent-proxy.md), and the local development and testing pages all sit under `/dev-guide/`.
* **A comparison of every credential path**: The [overview](dev-guide/overview.md#get-credentials-into-your-workload-at-runtime) compares Agent Proxy, the Edge SDK, the Aembit CLI, and the Edge API side by side, so you can pick one before you read its section.
* **Shared local development and testing pages**: One page covers [local development](dev-guide/integration/local-development.md) and one covers [testing and debugging](dev-guide/integration/testing.md) across every integration path.

Every URL under the old `/api-guide/` and `/cli-guide/` paths redirects to its new location, so existing bookmarks and links keep working.

### Aembit Edge SDKs for TypeScript and Python now available

The Aembit Edge SDKs are language libraries that wrap the [Aembit Edge API](dev-guide/api/edge/overview.md). Your application authenticates a workload and retrieves credentials through a few method calls, so you write no HTTP requests and manage no token lifecycles. The SDK runs inside your application process, so there’s nothing extra to deploy or operate alongside it.

Use an SDK when you can change the application’s source and you want credential retrieval to be explicit in your code. It suits environments where running a proxy next to the workload isn’t practical, such as serverless functions, ephemeral CI containers, and platforms where you don’t control the runtime.

**What’s new:**

* **TypeScript and Python libraries**: Install the SDK from its language package registry and call it from your application. The [SDK repository](https://github.com/Aembit/edge-sdks) carries the installation steps, the developer reference, and runnable examples.
* **Workload attestation, token lifecycle, and credential retrieval**: The SDK collects identity evidence from the environment it runs in, caches the resulting access token in memory, refreshes that token before it expires, and requests the credential your application needs.
* **Seven supported Trust Providers**: Attest with AWS Metadata Service, AWS Role, Azure Instance Metadata Service, GCP Identity Token, GitHub, GitLab, or OIDC ID Token. For per-language coverage, see [Edge SDK Trust Providers](reference/support-matrix.md#edge-sdk-trust-providers).
* **Integration guides for four platforms**: Follow a worked setup for [AWS EC2](dev-guide/sdk/edge/integrations/aws-ec2.md), [AWS Lambda](dev-guide/sdk/edge/integrations/aws-lambda.md), [GCP Cloud Run](dev-guide/sdk/edge/integrations/gcp-cloud-run.md), or [Vercel OIDC](dev-guide/sdk/edge/integrations/vercel-oidc.md).

The console-side configuration matches every other Aembit access path. You configure a Trust Provider to verify the workload’s identity and a Credential Provider to supply the credential. An Access Policy then authorizes a specific Client Workload to reach a specific Server Workload.

To retrieve your first credential, see the [Edge SDK quickstart](dev-guide/sdk/edge/quickstart.md). For the full overview, see [Aembit Edge SDKs](dev-guide/sdk/edge/overview.md).

### Aembit CLI now available as a glibc-linked Linux binary

Aembit CLI 1.34.5772 adds a second Linux amd64 archive, `aembit_agent_cli_linux_amd64_glibc_<version>.tar.gz`, that links dynamically against glibc 2.28 or newer. The default archive still links statically against musl, runs on amd64 and arm64 with no dependency on the host C library, and remains the recommended download.

If the default build has worked for you, keep using it. The glibc build is for organizations whose policy requires glibc, and for working around rare bugs in the default build. For example, the default build reports `failed to lookup address information` on some hosts where `dig` and `curl` resolve the same name, because musl rejects a DNS resolver response that glibc tolerates. See [DNS lookup fails with `failed to lookup address information`](dev-guide/cli/troubleshooting.md#dns-lookup-fails-with-failed-to-lookup-address-information) for that case. The glibc build is amd64 only, and it doesn’t run on Alpine Linux or in distroless images. See [Choose a Linux build](dev-guide/cli/usage/setup.md#choose-a-linux-build) for the full comparison.

### Edge components dependency security updates

Agent Proxy 1.34.5755 and Aembit CLI 1.34.5772 fix two published Rust advisories in bundled third-party libraries: [RUSTSEC-2026-0190](https://rustsec.org/advisories/RUSTSEC-2026-0190.html) in `anyhow` and [RUSTSEC-2026-0204](https://rustsec.org/advisories/RUSTSEC-2026-0204.html) in `crossbeam-epoch`. The same build upgrades the `quinn`, AWS SDK, `pcap`, and `postgres-protocol` dependencies. Agent Injector 1.34.433 fixes [RUSTSEC-2026-0185](https://rustsec.org/advisories/RUSTSEC-2026-0185.html) in `quinn-proto`.

These updates shipped in the same build as the [Edge components release with Agent Proxy fixes and expanded workload events](https://docs.aembit.io/changelog/entry/2026-08-27-edge-components-release-with-agent-proxy-fixes-and-expanded-workload-events).

### Edge components release with Agent Proxy fixes and expanded workload events

Aembit has released new versions of the following components and packages:

* Agent Proxy 1.34.5755
* AWS Lambda Extension 1.34.175
* Agent Injector 1.34.433
* Sidecar Init 1.34.138
* Helm Chart 1.34.566
* AWS ECS Terraform 1.34.1
* Aembit CLI 1.34.5772

For the latest available versions of these components, see the [Edge Components Supported Versions](reference/edge-components/edge-component-supported-versions.md) page.

Key Updates:

* **Workload events carry the authorization chain**: Agent Proxy events now include the [`authorizationChain`](user-guide/audit-report/workload-events/reference.md#top-level-fields) field, an ordered list of the context IDs of the authorization events behind the request, so you can trace an event back to the directive and credential retrievals that authorized it.
* **Aembit-generated responses keep their outcome fields**: When Aembit answers a request in place of the Server Workload, the response event now reports the generated response’s `severity`, `outcome.result`, and `outcome.reason`. Earlier versions cleared those fields between the request and the response, so the event recorded an incomplete outcome.
* **Windows installer honors the gRPC keep-alive properties**: The MSI now applies `AEMBIT_TENANT_GRPC_PING_INTERVAL_SECS` and `AEMBIT_TENANT_GRPC_PING_TIMEOUT_SECS` when you pass them as install-time properties. See [Configuring gRPC keep-alives](user-guide/deploy-install/virtual-machine/windows/agent-proxy-install-windows.md#configuring-grpc-keep-alives).
* **Linux installer validates Agent Proxy arguments**: The Linux installer script now checks Agent Proxy arguments before it installs, so an invalid value in a numeric environment variable such as `AEMBIT_HTTP_IDLE_TIMEOUT_SECS` is reported at install time.
* **Container image updates**: Agent Injector and Sidecar Init move to a newer Debian base image, and AWS Lambda Extension updates its Go version.

Dependency security fixes also shipped in this build. See [Edge components dependency security updates](https://docs.aembit.io/changelog/entry/2026-08-27-edge-components-dependency-security-updates).

## 2026-08-19

### Google Cloud Identity-Aware Proxy (IAP) JWT Trust Provider now available

Aembit now offers a Google Cloud Identity-Aware Proxy (IAP) JWT Trust Provider. It validates the signed `x-goog-iap-jwt-assertion` token that IAP forwards to your workloads on Google Kubernetes Engine (GKE), Cloud Run, and App Engine after IAP authenticates a user.

Use this Trust Provider instead of the OIDC ID Token Trust Provider when IAP authenticates users in front of your workloads. IAP issues signed JWTs, but they aren’t standard OpenID Connect (OIDC) ID tokens. Google publishes the IAP signing keys at a static JWKS endpoint with no discovery document, and the audience identifies a Google Cloud resource rather than an OIDC client ID.

Aembit resolves Google’s signing key at validation time rather than from a preconfigured key set. It reads the key ID from the token, then retrieves the matching public key from Google’s IAP key endpoint. Aembit caches that key in memory, so your workload only forwards the token.

**What’s new:**

* **GCP IAP JWT Trust Provider type**: Supply the IAP audience that identifies the Google Cloud resource IAP protects. Aembit pins the issuer and resolves the signing keys, so you don’t upload a key or enter a discovery URL.
* **Match rules on IAP token claims**: Authorize on `aud`, `iss`, `sub`, and `email`, or on any additional claim IAP signs using `custom_claim`. The `aud` attribute supports wildcards, so one rule can cover more than one backend service in the same project.
* **Terraform support**: Manage the Trust Provider through the Aembit Terraform provider with the `aembit_trust_provider` resource.

This Trust Provider validates the user identity that IAP asserts. To validate the identity of a workload running in Google Cloud, use the [GCP Identity Token Trust Provider](user-guide/access-policies/trust-providers/gcp-identity-token-trust-provider.md) instead.

For the audience formats, match rule attributes, and configuration steps, see [Google Cloud Identity-Aware Proxy (IAP) JWT Trust Provider](user-guide/access-policies/trust-providers/gcp-iap-jwt-trust-provider.md).

## 2026-08-18

### MCP Identity Gateway 1.34.5733 release

Aembit has released [MCP Identity Gateway](user-guide/deploy-install/mcp-identity-gateway/overview.md) version 1.34.5733.

For the latest available versions of these components, see the [Edge Components Supported Versions](reference/edge-components/edge-component-supported-versions.md) page.

Key Updates:

* **Higher fanout timeout defaults**: The Gateway now waits longer for your assigned MCP servers on most of the MCP methods it fans out, which gives a server that’s still starting up, or one answering from a cold cache, room to respond. A self-hosted Gateway can override any of these timeouts as a last resort; see [MCP Identity Gateway raises fanout timeout defaults](https://docs.aembit.io/changelog/entry/2026-08-18-mcp-identity-gateway-raises-fanout-timeout-defaults).
* **Nine new Prometheus metrics**: The Gateway’s metrics endpoint reports on upstream fanout behavior, authentication failures, JWKS refreshes, session cleanup, and readiness probe stability.
* **Every Gateway metric documented**: The metrics reference now lists all 20 MCP-specific metrics the Gateway exposes, with the type and labels for each and the values each label takes. See [Prometheus metrics](user-guide/deploy-install/mcp-identity-gateway/reference-mcp-gateway.md#prometheus-metrics).

### MCP Identity Gateway raises fanout timeout defaults

[MCP Identity Gateway](user-guide/deploy-install/mcp-identity-gateway/overview.md) 1.34.5733 raises how long the Gateway waits for your assigned MCP servers when it fans a request out to them. The earlier defaults were short enough that a server still starting up, or one answering from a cold cache, could run past them.

| Timeout                            | 1.33.5654 | 1.34.5733 |
| ---------------------------------- | --------- | --------- |
| `initialize`                       | `3s`      | `10s`     |
| `notifications_initialized`        | `500ms`   | `1s`      |
| `tools_list`                       | `1s`      | `3s`      |
| `proactive_tools_list`             | `3s`      | `5s`      |
| `resources_list`                   | `1s`      | `3s`      |
| `reinit_initialize`                | `10s`     | `10s`     |
| `reinit_notifications_initialized` | `500ms`   | `1s`      |
| `reinit_tools_list`                | `1s`      | `5s`      |

`reinit_initialize` is the one timeout this build leaves unchanged.

The new defaults require no configuration and apply to both deployment models as soon as you upgrade. They’re high enough that an AI client typically reaches its own timeout before the Gateway reaches one of these, so most deployments never need to change them.

A self-hosted Gateway can override any of these timeouts as a last resort, using the `AEMBIT_MCP_GATEWAY_TIMEOUT` environment variable. The installer doesn’t forward this variable to the running service, so setting it takes a systemd drop-in. For the accepted names, the value format, and the drop-in, see [MCP Identity Gateway environment variables](user-guide/deploy-install/mcp-identity-gateway/env-vars-mcp-gateway.md#aembit_mcp_gateway_timeout). Aembit sets these values for you on the Aembit-managed service.

These updates shipped in the same build as the [MCP Identity Gateway 1.34.5733 release](https://docs.aembit.io/changelog/entry/2026-08-18-mcp-identity-gateway-1-34-5733-release).

## 2026-08-11

### Aembit Access Token lifetime has a 300 second minimum

The **Lifetime** field on an Aembit Access Token Credential Provider now accepts a minimum of 300 seconds (5 minutes). The Admin UI, the Aembit API, and the Aembit Terraform provider all enforce the same minimum, so any shorter value fails validation wherever you set it.

The default lifetime stays at 900 seconds (15 minutes).

See [Aembit Access Token](user-guide/access-policies/credential-providers/aembit-access-token.md).

### Certificates replaces TLS Decrypt in Edge Components

The Aembit Admin UI page for managing your Aembit Tenant Root CA and your Standalone Certificate Authorities is now named **Certificates**, replacing the earlier name **TLS Decrypt**. Find it under **Edge Components** in the top ribbon menu.

The page keeps all its existing fields, and the TLS Decrypt capability keeps its name. Only the page label changed, so the **TLS** checkbox on a Server Workload and every other TLS Decrypt setting keep their current locations.

See [Configure TLS Decrypt](user-guide/deploy-install/advanced-options/tls-decrypt/configure-tls-decrypt.md) and [How to configure a Standalone CA](user-guide/deploy-install/advanced-options/tls-decrypt/configure-tls-decrypt-standalone-ca.md).

## 2026-08-07

### Content Security now available

Aembit now offers [Content Security](user-guide/access-policies/content-security/overview.md), an Access Policy component that inspects content and enforces the verdict an inspection service returns. Content Security inspects Model Context Protocol (MCP) traffic that flows through the Aembit [MCP Identity Gateway](user-guide/deploy-install/mcp-identity-gateway/overview.md).

In the Access Policy Builder, Content Security is an optional component positioned between Access Conditions and Credential Providers. It adds inspection to the request path without changing how the policy’s other components authorize access.

**What’s new:**

* **Content Security Access Policy component**: Add Content Security to a policy to inspect the MCP traffic that policy governs. Inspection applies only to the policies you add it to.
* **CrowdStrike AIDR integration**: [CrowdStrike AI Detection and Response (AIDR)](user-guide/access-policies/content-security/crowdstrike-aidr/overview.md) inspects tool listings, tool call inputs, and tool call outputs, and returns an allow, block, or transform verdict that Aembit enforces. AIDR requires an active CrowdStrike AIDR for Agents subscription.
* **Inspection decisions in your events**: Access Authorization Events record the Content Security component a policy identified, and MCP Workload Events record the decision AIDR returned, including CrowdStrike’s request ID for correlating an event with the CrowdStrike console.

CrowdStrike AIDR Content Security is separate from the [CrowdStrike Access Condition](user-guide/access-policies/access-conditions/crowdstrike.md), which evaluates endpoint posture. Configuring one doesn’t enable the other.

To add the component to a policy, see [Add CrowdStrike AIDR to a policy](user-guide/access-policies/content-security/crowdstrike-aidr/add-to-policy.md).

### MCP Identity Gateway 1.33.5654 release

Aembit has released [MCP Identity Gateway](user-guide/deploy-install/mcp-identity-gateway/overview.md) version 1.33.5654.

For the latest available versions of these components, see the [Edge Components Supported Versions](reference/edge-components/edge-component-supported-versions.md) page.

For each [Content Security](user-guide/access-policies/content-security/overview.md) decision, the Gateway records a single MCP Workload Event carrying the severity, outcome result, and outcome reason.

This build also broadens the MCP traffic that Content Security inspects; see [MCP Identity Gateway content inspection coverage](https://docs.aembit.io/changelog/entry/2026-08-07-mcp-identity-gateway-content-inspection-coverage). For Content Security inspection of MCP traffic, use MCP Identity Gateway version 1.33.5654 or later.

### MCP Identity Gateway content inspection coverage

A security update for the MCP Identity Gateway broadens the MCP traffic that [Content Security](user-guide/access-policies/content-security/overview.md) inspects.

The Gateway normalizes responses that MCP servers stream as server-sent events before inspection, so Content Security inspects those responses along with the rest of the session. The Gateway also applies the policy’s **Fail Open on Error** setting to requests it can’t read and to protocol errors. A policy set to fail closed blocks those requests.

These updates shipped in the same build as the [MCP Identity Gateway 1.33.5654 release](https://docs.aembit.io/changelog/entry/2026-08-07-mcp-identity-gateway-1-33-5654-release).

## 2026-08-05

### AWS Application Load Balancer JWT Trust Provider now available

Aembit now offers an AWS Application Load Balancer JWT Trust Provider. It validates the signed `x-amzn-oidc-data` token that an AWS Application Load Balancer (ALB) forwards to your workloads after the ALB authenticates a user session with OpenID Connect (OIDC).

Use this Trust Provider instead of the OIDC ID Token Trust Provider when your workloads sit behind an ALB that authenticates users. ALB tokens require the AWS Application Load Balancer JWT Trust Provider.

Aembit resolves the signing key at validation time rather than from a preconfigured key set. It reads the AWS region and key ID from the token, validates the region before it contacts AWS, then retrieves and caches the matching regional public key. Aembit rotates those keys for you, so your workload only forwards the token.

**What’s new:**

* **AWS Application Load Balancer JWT Trust Provider type**: Select the type and add your match rules. The type has no configuration fields of its own, so you don’t set a region or upload a signing key.
* **Match rules on ALB token claims**: Authorize on `aud`, `iss`, `sub`, and `email`, or on any additional claim your identity provider forwards through the ALB using `custom_claim`. You can add more than one `custom_claim` rule to a single Trust Provider.
* **Terraform support**: Manage the Trust Provider through the Aembit Terraform Provider with the `aembit_trust_provider` resource.

This Trust Provider works with Aembit Edge. Agent Controller support isn’t available yet.

For the match rule attributes, configuration steps, and troubleshooting, see [AWS Application Load Balancer JWT Trust Provider](user-guide/access-policies/trust-providers/aws-alb-jwt-trust-provider.md).

## 2026-07-28

### MCP Identity Gateway 1.33.5547 release

Aembit has released [MCP Identity Gateway](user-guide/deploy-install/mcp-identity-gateway/overview.md) version 1.33.5547.

For the latest available versions of these components, see the [Edge Components Supported Versions](reference/edge-components/edge-component-supported-versions.md) page.

Key Updates:

* **More reliable upstream reconnection**: When the Gateway reestablishes a connection to an MCP server, it now refreshes the available tools as part of that reconnection, and reports a clear error if the reconnection doesn’t succeed.
* **Deployment modes documented**: The MCP Identity Gateway concepts page now describes the two ways to run the Gateway: self-hosted on your own host, or managed by Aembit at your per-Tenant endpoint. The traffic flow and policy model are identical in both. See [MCP Identity Gateway concepts](user-guide/deploy-install/mcp-identity-gateway/concepts-mcp-gateway.md).

Dependency security updates also shipped in this build; see [MCP Identity Gateway dependency security updates](https://docs.aembit.io/changelog/entry/2026-07-28-mcp-identity-gateway-dependency-security-updates).

### MCP Identity Gateway dependency security updates

A security update for the MCP Identity Gateway advances two bundled third-party libraries to their latest patched releases, keeping the Gateway current with published advisories.

These updates shipped in the same build as the [MCP Identity Gateway 1.33.5547 release](https://docs.aembit.io/changelog/entry/2026-07-28-mcp-identity-gateway-1-33-5547-release).

## 2026-07-08

### Agent Controller container security hardening

Agent Controller 1.32.3541 includes security hardening for the container image that reduces its attack surface and strengthens supply chain integrity. The image now ships with a bundled software bill of materials for greater transparency into its contents.

### OpenAI WIF Credential Provider now available

Aembit now offers an OpenAI Workload Identity Federation (WIF) Credential Provider. Your workloads can call the OpenAI API with short-lived tokens that Aembit obtains on their behalf instead of long-lived API keys.

Aembit acts as a trusted OIDC issuer. You register Aembit as a Workload Identity Provider in OpenAI. Then, whenever an Access Policy grants a workload access, Aembit exchanges its issued assertion for a short-lived OpenAI access token.

**What’s new:**

* **OpenAI WIF Credential Provider type**: Configure the Identity Provider ID, service account, and optional audience that Aembit presents when it exchanges tokens with OpenAI.
* **Built-in connection verification**: Use **Verify** to confirm the trust between Aembit and your OpenAI federation setup before you put the Credential Provider into an Access Policy.
* **Terraform support**: Manage the OpenAI WIF Credential Provider through the Aembit Terraform Provider.

For setup instructions, see [Configure an OpenAI WIF Credential Provider](user-guide/access-policies/credential-providers/openai-workload-identity-federation.md).

## 2026-06-30

### MCP Identity Gateway sessions can persist across restarts

A self-hosted [MCP Identity Gateway](user-guide/deploy-install/mcp-identity-gateway/overview.md) can now keep its MCP sessions in Valkey instead of in process memory. Persisted sessions survive a service restart or an upgrade, and more than one Gateway instance can share them.

Set `AEMBIT_VALKEY_URL` on the install command to enable it. The Gateway keeps sessions in memory when you leave that variable unset, which remains the default. An in-memory store ends every open session on restart. `AEMBIT_MCP_SESSION_IDLE_TTL_SECS` sets how long an unused session lives, and it defaults to 12 hours for both store types.

Sessions hold MCP protocol state, not authentication state. The Gateway still validates every request’s token and evaluates Access Policies per request, so persistence doesn’t extend anyone’s access.

This change requires no configuration if you use the Aembit-managed service or want the in-memory default. It ships in MCP Identity Gateway 1.32.5098, alongside the session ID binding in this build; see the [session IDs bound to the authenticated user](https://docs.aembit.io/changelog/entry/2026-06-30-mcp-identity-gateway-session-ids-are-now-bound-to-the-authenticated-user) entry. For configuration and operational detail, see [Session persistence](user-guide/deploy-install/mcp-identity-gateway/session-persistence-mcp-gateway.md).

### Client ID Metadata Document (CIMD) identification now available

Aembit can now identify MCP client workloads with a Client ID Metadata Document (CIMD). A CIMD is a JSON document the client hosts at a public HTTPS URL, and the URL itself serves as the client’s identity. When the client presents the URL-formatted `client_id`, Aembit fetches the document and validates the client’s metadata, so the client can authenticate without Dynamic Client Registration.

CIMD has reached general availability, and Aembit enables it by default. To identify a Client Workload by its CIMD URL, see [CIMD Client ID](user-guide/access-policies/client-workloads/identification/cimd-client-id.md).

### MCP Identity Gateway session IDs are now bound to the authenticated user

[MCP Identity Gateway](user-guide/deploy-install/mcp-identity-gateway/overview.md) now binds each session ID to the authenticated user, closing a privilege-escalation gap where one user’s session could carry over to another identity.

This change requires no configuration. It ships in MCP Identity Gateway 1.32.5098, alongside the workload event correlation and platform fixes in this build; see the [event correlation and platform fixes entry](https://docs.aembit.io/changelog/entry/2026-06-30-mcp-identity-gateway-event-correlation-and-platform-fixes).

### MCP Identity Gateway event correlation and platform fixes

This update brings event correlation to [MCP Identity Gateway](user-guide/deploy-install/mcp-identity-gateway/overview.md) workload events, along with fixes across Credential Provider, Access Policy, and Client Workload workflows.

This update includes:

* MCP Identity Gateway 1.32.5098

For the latest available versions of these components, see the [Edge Components Supported Versions](reference/edge-components/edge-component-supported-versions.md) page.

Key Updates:

* **Workload event correlation**: MCP Identity Gateway [workload events](user-guide/audit-report/workload-events/overview.md) now carry the client and server workload identity (name and id), so you can tie an event to a specific workload in the Cloud UI without mapping it by hand.
* **Azure OAuth Credential Provider verification**: Resolved an issue that caused Azure OAuth Credential Provider verification to fail after a recent build, so these providers verify successfully again.
* **Access Condition URL validation**: The Endpoint and Token Endpoint URL fields for Access Condition integrations now show the correct “Invalid Value” message instead of a “Required” message.
* **Concurrent policy edits**: Deleting an Access Policy at the same time as a patch request no longer produces errors, preserving data integrity during high-volume policy updates.
* **CIMD authorization flow**: Fixed an issue where an OIDC session expiring mid-flow could clear the Client Workload cookie and interrupt authorization.
* **Form validation focus**: Saving a form with an invalid URL field now scrolls that field into view across entity editors, and External ID tooltips no longer get cut off in long list views.

Session IDs are now bound to the authenticated user in this build, closing a privilege-escalation gap; see the [Security entry](https://docs.aembit.io/changelog/entry/2026-06-30-mcp-identity-gateway-session-ids-are-now-bound-to-the-authenticated-user). Client ID Metadata Document (CIMD) identification also reached general availability in this update; see the [announcement](https://docs.aembit.io/changelog/entry/2026-06-30-client-id-metadata-document-cimd-identification-now-available).

## 2026-06-26

### MCP User-Based Access Token callback URL format

MCP User-Based Access Token Credential Providers now generate their read-only callback URL in the form `https://<tenantId>.id.<region>.aembit.io/mcp-auth/userauth/<cp-id>/callback`.

**What this means for you:**

* **Existing Credential Providers** - Providers created before this change keep their original callback URL, which takes the form `https://<tenantId>.mcp.<region>.aembit.io/userauth/<cp-id>/callback`. Updating a provider doesn’t regenerate its callback URL. Both formats remain valid, so you don’t need to take any action or re-register anything with your MCP server vendor.
* **New Credential Providers** - Aembit assigns the callback URL when you save the Credential Provider. Save the provider first, then copy the read-only value and register it with the MCP server vendor.

Always copy the exact value shown in the Credential Provider rather than constructing the URL yourself.

For details, see [Configure MCP User-Based Access Token Credential Provider](user-guide/access-policies/credential-providers/mcp-user-based-access-token.md).

## 2026-06-23

### Resource Set deletion now available

You can now delete a custom Resource Set when you no longer need it. Deletion is a cascading, all-or-nothing operation: Aembit removes the Resource Set and every entity it contains in a single action that either completes fully or rolls back.

**What’s new:**

* **Cascading deletion**: Deleting a Resource Set also deletes everything it contains, including Access Policies, Client and Server Workloads, Trust Providers, Credential Providers, Access Conditions, and Standalone CAs, leaving no orphaned data behind.
* **Safeguards against accidental loss**: Deletion requires Write access to the Resource Set or an administrator role, and Aembit asks you to confirm the action in a dialog that lists what it removes. You can’t delete the Default Resource Set.
* **Audit coverage**: Aembit records an audit entry for each deleted entity, plus one for the Resource Set deletion itself, so the operation stays traceable through [Audit Logs](user-guide/audit-report/audit-logs.md) and Log Streams.
* **Terraform lifecycle management**: Manage the same lifecycle as code with the `aembit_resource_set` resource. Destroying an `aembit_resource_set` performs the identical cascading delete, and the `resource_set_id` argument assigns or filters entities by Resource Set.

For the full workflow and its effects, see [Delete a Resource Set](user-guide/administration/resource-sets/delete-resource-set.md). For managing Resource Sets as code, see [Scaling Aembit with Terraform](get-started/concepts/scaling-terraform.md).

## 2026-06-12

### Claude API WIF Credential Provider now available

Aembit now offers a Claude Workload Identity Federation (WIF) Credential Provider. Your workloads can call the Claude API with short-lived tokens that Aembit obtains on their behalf instead of long-lived API keys.

Aembit acts as a trusted OIDC issuer. You register Aembit as a federation issuer in Claude Console. Then, whenever an Access Policy grants a workload access, Aembit exchanges its issued assertion for a short-lived Claude access token.

**What’s new:**

* **Claude WIF Credential Provider type**: Configure the federation rule, organization, service account, and optional workspace from your Claude Console, plus the requested scope and token lifetime.
* **Built-in connection verification**: Use **Verify** to confirm the trust between Aembit and your Claude federation rule before you put the Credential Provider into an Access Policy.
* **Terraform support**: Manage the Claude WIF Credential Provider through the Aembit Terraform Provider.

For setup instructions, see [Configure a Claude WIF Credential Provider](user-guide/access-policies/credential-providers/claude-workload-identity-federation.md).

### Edge components release with Aembit CLI and Secrets Operator updates

Aembit has released new versions of the following components and packages:

* Aembit Secrets Operator
* Aembit Secrets Operator Helm chart

For the latest available versions of these components, see the [Edge Components Supported Versions](reference/edge-components/edge-component-supported-versions.md) page.

Key Updates:

* **AWS and Kubernetes Trust Provider attestation in Aembit CLI**: The `aembit credentials get` command now supports the AWS Metadata Service, AWS Role, and Kubernetes Service Account Trust Providers. Aembit CLI gathers attestation data from the local environment—instance metadata, an STS `GetCallerIdentity` request, or the projected service account token—so an externally supplied [`--id-token`](dev-guide/cli/reference/credentials-get.md#--id-token) isn’t needed for these Trust Providers. The [`--deployment-model`](dev-guide/cli/reference/credentials-get.md#--deployment-model) option now accepts `vm`, `kubernetes`, `ecs_fargate`, and `lambda_container`. The AWS Role Trust Provider requires this option.
* **Aembit Secrets Operator credential type support**: Secrets Operator 1.32.322 now retrieves any credential type your Access Policy issues, not just HashiCorp Vault tokens. See [Aembit Secrets Operator now supports more credential types](https://docs.aembit.io/changelog/entry/2026-06-04-aembit-secrets-operator-now-supports-more-credential-types).

## 2026-06-04

### Aembit Secrets Operator now supports more credential types

**Aembit Secrets Operator 1.32.322** is now available.

[Secrets Operator](user-guide/deploy-install/kubernetes/aso/overview.md) now retrieves any credential type your Access Policy issues—not just HashiCorp Vault tokens. A new `credentialType` field on the `AembitSecretRefreshSchedule` resource selects which Credential Provider type Aembit uses, and the managed Kubernetes Secret mirrors the Aembit Edge API credentials response for that provider.

* **New `credentialType` field**: Choose `OAuthToken` (the default), `ApiKey`, `UsernamePassword`, `AwsStsFederation`, or `GoogleWorkloadIdentityFederation`. Each type writes its own Secret data keys—for example, `UsernamePassword` produces `username` and `password`, and `AwsStsFederation` produces `awsAccessKeyId`, `awsSecretAccessKey`, and `awsSessionToken`. See [Credential types and Secret data keys](user-guide/deploy-install/kubernetes/aso/reference.md#credential-types-and-secret-data-keys).
* **Backward compatible**: Schedules that omit `credentialType` keep writing a single `token` key, so existing HashiCorp Vault and cert-manager configurations need no change.
* **Clearer mismatch errors**: When `credentialType` doesn’t match the configured Credential Provider, the schedule reports `Aembit Edge API returned a credentials response with no populated fields` instead of writing a blank Secret.
* **AWS credential redaction**: Secrets Operator redacts the AWS access key, secret access key, and session token values from its debug logs.

### Agent Proxy security update for a DNS library vulnerability

A security update for Agent Proxy resolves a vulnerability in a bundled DNS library.

This fix shipped in the same build as the [Edge components release with reliability, CLI, and MCP enhancements](https://docs.aembit.io/changelog/entry/2026-06-04-edge-components-release-with-reliability-cli-and-mcp-enhancements), across the Agent Proxy container, virtual machine package, and AWS Lambda deployments.

### Edge components release with reliability and CLI enhancements

Aembit has released new versions of the following components and packages:

* Helm Chart
* Terraform ECS module
* VM Agent Proxy package
* VM Agent Controller package
* Agent CLI
* AWS Lambda Extension
* AWS Lambda Layer
* Agent Proxy

For the latest available versions of these components, see the [Edge Components Supported Versions](reference/edge-components/edge-component-supported-versions.md) page.

Key Updates:

* **X.509-SVID retrieval through Aembit CLI**: The `aembit credentials get` command now accepts [`--client-tls-private-key`](dev-guide/cli/reference/credentials-get.md#--client-tls-private-key) to retrieve a SPIFFE-compliant X.509-SVID certificate from the existing [X.509-SVID Credential Provider](user-guide/access-policies/credential-providers/about-spiffe-x509-svid.md). You supply a PEM-encoded private key; Aembit CLI generates the certificate signing request locally and returns the signed chain in `CLIENT_CERT_CHAIN`, and the private key never leaves the local machine.
* **Configurable gRPC keep-alives**: Two optional environment variables, [`AEMBIT_TENANT_GRPC_PING_INTERVAL_SECS` and `AEMBIT_TENANT_GRPC_PING_TIMEOUT_SECS`](reference/edge-components/edge-component-env-vars.md#agent-proxy-environment-variables), let Agent Proxy send keep-alives on its connection to your Tenant so it detects a dead connection and reconnects faster. They’re off by default and useful for networks, such as a Secure Web Gateway, that stall idle connections.
* **CA certificate configuration for the Cloud connection**: [`AGENT_TRUST_PATH`](reference/edge-components/edge-component-env-vars.md#agent_trust_path) again lets you supply a custom CA certificate for the Agent Proxy’s connection to the Aembit Cloud, which is useful when an inspecting proxy terminates TLS on outbound traffic.
* **Configurable HTTP idle timeout on Windows**: The Windows installer now exposes [`AEMBIT_HTTP_IDLE_TIMEOUT_SECS`](reference/edge-components/edge-component-env-vars.md#aembit_http_idle_timeout_secs), letting you tune the idle timeout for HTTP/1.1 connections handled by the Agent Proxy.
* **Caching enhancements**: Improvements to credential caching across the Agent Proxy and Aembit CLI.
* **Improved upstream proxy diagnostics**: When the Agent Proxy can’t reach a configured upstream HTTP proxy, logs now include the full error source chain instead of a generic connection error, making a misconfigured upstream proxy easier to diagnose.
* **General improvements**: Stability, robustness, and dependency updates across edge components, including improved hardware-identification handling during process identification on Linux virtual machines.

### MCP Identity Gateway 1.32.5006 release

Aembit has released [MCP Identity Gateway](user-guide/deploy-install/mcp-identity-gateway/overview.md) version 1.32.5006.

For the latest available versions of these components, see the [Edge Components Supported Versions](reference/edge-components/edge-component-supported-versions.md) page.

Key Updates:

* **MCP ping support**: The Gateway now supports the MCP `ping` method, responding to client connection-health checks per the MCP specification.
* **Authorization chain in workload events**: MCP Identity Gateway workload events now include an ordered authorization chain, the ordered list of authorization-event context IDs behind each request. The chain spans both sides of the proxied connection, carried from the access token on the agent-to-Gateway side and forward through the directive and credential calls on the Gateway-to-MCP-server side, so you can confirm both belong to the same logical request. Take any context ID from the chain and filter your authorization events on it to trace a request end-to-end, from the traffic you observe back to the token, directive, and credential decisions that allowed it.
* **Microsoft Copilot Studio compatibility**: Fixed an issue where some tools could fail to load for Microsoft Copilot Studio clients.
* **Health endpoint status**: The Gateway’s health endpoint now returns an HTTP 503 status when the Gateway is unhealthy, so orchestrators and load balancers can detect an unhealthy Gateway accurately.
* **More reliable session timing**: Session lifetimes now track wall-clock time, so sessions expire and renew correctly across events such as a host suspend and resume.

## 2026-06-03

### Aembit CLI now retrieves X.509-SVID certificates

Aembit CLI now retrieves SPIFFE-compliant X.509-SVID certificates directly from the X.509-SVID Credential Provider.

Aembit has released new versions of the following components and packages:

* Aembit CLI

For the latest available versions of these components, see the [Edge Components Supported Versions](reference/edge-components/edge-component-supported-versions.md) page.

Key Updates:

* **X.509-SVID retrieval through Aembit CLI**: The `aembit credentials get` command now accepts [`--client-tls-private-key`](dev-guide/cli/reference/credentials-get.md#--client-tls-private-key) to retrieve a SPIFFE-compliant X.509-SVID certificate from the existing [X.509-SVID Credential Provider](user-guide/access-policies/credential-providers/about-spiffe-x509-svid.md). You supply a PEM-encoded private key. Aembit CLI generates the Certificate Signing Request (CSR) locally, submits it through the credential retrieval flow, and returns the signed certificate chain in `CLIENT_CERT_CHAIN`. The private key never leaves the local machine.

## 2026-05-21

### End-to-end mTLS between Client and Server Workloads with SPIFFE X.509-SVID certificates

Introducing end-to-end mutual TLS (mTLS) between Client Workloads and Server Workloads using SPIFFE-compliant X.509-SVID certificates.

Aembit has released new versions of the following components and packages:

* Agent Proxy
* Cloud (Tenant UI + API)
* EdgeAPI
* Terraform Provider
* Helm Chart
* Terraform ECS module
* AWS Lambda Extension
* AWS Lambda Layer

For the latest available versions of these components, see the [Edge Components Supported Versions](reference/edge-components/edge-component-supported-versions.md) page.

Key Updates:

* **Agent Proxy outbound mTLS with X.509-SVID**: Agent Proxy can now establish outbound mTLS connections to Server Workloads using SPIFFE-compliant X.509-SVID certificates, with no application code changes required.
* **mTLS Authentication method for Server Workloads**: A new authentication method, **mTLS Authentication** with the `x509 Certificate` scheme, lets Server Workloads validate the client certificate that Agent Proxy presents during the mTLS handshake.
* **X.509-SVID Credential Provider**: A new Credential Provider type that issues SPIFFE-compliant X.509 certificates. This release’s Agent Proxy update is what consumes them for outbound mTLS to Server Workloads.

***

Agent Proxy can now establish outbound mTLS connections to Server Workloads using SPIFFE-compliant X.509-SVID certificates, enabling certificate-based workload-to-workload authentication without application code changes.

**What’s new:**

* **In-memory private key**: Agent Proxy generates an ECDSA key pair in memory for each X.509-SVID certificate. The private key is never written to disk and is never transmitted to Aembit Cloud.
* **Automatic rotation at 80% of certificate lifetime**: Agent Proxy refreshes the certificate well before expiration, generating a new key pair on each refresh. In-progress mTLS connections continue using the prior certificate until they close.
* **mTLS Authentication for Server Workloads**: A new Server Workload authentication method (**mTLS Authentication** with the `x509 Certificate` scheme) lets the Server Workload side validate the X.509-SVID certificate that Agent Proxy presents during the handshake.

For the end-to-end workflow and procedure, see [Enable mTLS on a Server Workload](user-guide/access-policies/server-workloads/enable-mtls.md). For the authentication-method catalog, see [Authentication methods and schemes](user-guide/access-policies/server-workloads/authentication.md).

***

Aembit is introducing a new **X.509-SVID Credential Provider** type that issues SPIFFE-compliant X.509 certificates to Client Workloads, signed by an Aembit Standalone CA.

**What’s new:**

* **SPIFFE-compliant identity in the URI Subject Alternative Name (SAN)**: Every issued certificate embeds the workload’s SPIFFE ID as a URI SAN, so SPIFFE-aware Server Workloads can authenticate the Client Workload during the TLS handshake.
* **Literal or dynamic Subject and SPIFFE ID**: Configure either field with a fixed value or with template expressions that resolve at issuance time using workload attestation attributes.
* **Configurable Extended Key Usage**: Default to `id-kp-clientAuth` for outbound mTLS, or add `id-kp-serverAuth` to use the same certificate as a server credential.
* **Configurable certificate lifetime**: Set the lifetime in minutes (default 15). Agent Proxy automatically refreshes the certificate before expiration (typically at 80% of the configured lifetime).

For setup instructions, see [Create an X.509-SVID Credential Provider](user-guide/access-policies/credential-providers/spiffe-x509-svid.md). For concepts and the end-to-end issuance flow, see [About the X.509-SVID Credential Provider](user-guide/access-policies/credential-providers/about-spiffe-x509-svid.md).

## 2026-05-19

### Expanded MCP and AI IAM event coverage

Aembit has expanded the event coverage and reporting surfaces for troubleshooting MCP and AI IAM failures:

* **New `access.discovery` event type**: Access Authorization Events now include an `access.discovery` event that lists the Client Workloads and Server Workloads Aembit Cloud considered during evaluation. Use it to diagnose requests that match no workload or policy, or that match multiple. See [Access Discovery events](user-guide/audit-report/access-authorization-events.md#access-discovery-events).
* **User identity on MCP Workload Events**: MCP Workload Events now include a `userId` field at `application.mcp.userId` for flows that involve a human identity, such as MCP Authorization Server flows. The Workload Events view exposes a matching **User (MCP App Protocol only)** filter for per-user investigations and SIEM scoping.
* **Trust Provider failures emit at Error severity**: Trust Provider attestation failures in MCP flows now emit at `Error` severity rather than warning, so SIEM alerts that watch for `Error` events catch real authorization failures reliably.
* **Clearer expired-credential explanations**: The `access.credential` event’s reason now identifies which token expired and at which step, making it easier to decide between re-authentication, credential refresh, or Credential Provider reconfiguration.
* **MCP Authorization Tracing view**: A new live diagnostic view in the Reporting dashboard surfaces inbound authorization requests at the MCP Identity Gateway in real time, with the redirect URI, resource, matched Client Workload, and policy outcome for each request. See [MCP Authorization Tracing](user-guide/audit-report/mcp-authorization-tracing.md).

For an end-to-end investigation flow that uses these reporting surfaces together, see [Troubleshoot MCP and AI IAM access](user-guide/troubleshooting/mcp-ai-iam.md).

### MCP Identity Gateway 1.31.4955 release

Aembit has released [MCP Identity Gateway](user-guide/deploy-install/mcp-identity-gateway/overview.md) version 1.31.4955.

For the latest available versions of these components, see the [Edge Components Supported Versions](reference/edge-components/edge-component-supported-versions.md) page.

Key Updates:

* **Session deletion**: Support for deleting MCP sessions, enabling clients to explicitly end MCP Identity Gateway sessions when finished.
* **MCP-level error metrics**: New Prometheus metrics expose MCP protocol-level errors, giving operators visibility into request failures at the MCP layer.
* **Application-specific Prometheus metrics**: Additional Prometheus metrics scoped to the MCP Identity Gateway application for improved observability.

## 2026-05-15

### Aembit Secrets Operator CRDs graduate to v1

**Aembit Secrets Operator 1.31.314** is now available.

This release graduates the [Secrets Operator](user-guide/deploy-install/kubernetes/aso/overview.md) CRDs from `v1beta1` to `v1`. Use `apiVersion: aembit.io/v1` in your manifests—see the [Configuration Reference](user-guide/deploy-install/kubernetes/aso/reference.md) for the updated specs.

## 2026-05-12

### Aembit Secrets Operator now available

**Aembit Secrets Operator 1.31.298** is now available.

[Secrets Operator](user-guide/deploy-install/kubernetes/aso/overview.md) is a Kubernetes operator that authenticates to the Aembit platform and synchronizes credentials into Kubernetes Secrets. Applications consume managed secrets the same way they consume any other Kubernetes Secret.

Key capabilities in this release:

* **Kubernetes Service Account authentication**: Authenticate using the operator’s in-cluster ServiceAccount token, validated against the cluster’s OIDC endpoint. No per-cluster signing key required. Verified on Amazon EKS and K3s. See [Set up Secrets Operator](user-guide/deploy-install/kubernetes/aso/setup.md) for configuration.
* **OIDC symmetric key authentication**: Alternatively, authenticate using OIDC tokens with symmetric key signing (HS256) for custom claims and non-Kubernetes identity scenarios.
* **Proactive credential renewal**: Credentials refresh at 80% of their TTL, or sooner when you configure a shorter `refreshInterval`, ensuring applications always have a valid credential.
* **Multi-namespace install**: You can now use the same Helm release name across multiple namespaces on the same cluster without resource name conflicts.

### MCP Identity Gateway 1.31 release

Aembit has released [MCP Identity Gateway](user-guide/deploy-install/mcp-identity-gateway/overview.md) version 1.31.

Key Updates:

* **User identity on workload events**: The `userId` field now appears on `mcp.request` and `mcp.response` [workload events](user-guide/deploy-install/mcp-identity-gateway/reference-mcp-gateway.md#workload-events) when the MCP client is identified, making it easier to attribute MCP activity to authenticated users in audit reports.
* **Client-initiated session termination**: MCP clients can now end their session with the Gateway by sending an HTTP `DELETE` request to the `/mcp` endpoint, per [MCP specification section 2.5.5](https://modelcontextprotocol.io/specification/2025-03-26/basic/transports#session-management). See [Session management](user-guide/deploy-install/mcp-identity-gateway/reference-mcp-gateway.md#session-management) for the request contract.

## 2026-05-02

### Dynamic claims now support custom environment variables

Custom environment variables on Agent Proxy and Aembit CLI can now feed into OIDC and JWT-SVID dynamic claims, gated by an explicit allowlist.

**What’s new:**

* **`AEMBIT_ENV_VAR_ALLOWLIST`**: A new environment variable that defines which custom variables Agent Proxy and Aembit CLI may capture for use in dynamic claims. By default, Agent Proxy and Aembit CLI capture no custom variables.
* **Always-available Kubernetes variables**: `K8S_POD_NAME`, `K8S_NAMESPACE`, and `KUBERNETES_PROVIDER_ID` are now usable in dynamic claims regardless of the allowlist.

For setup instructions, see [Configure custom environment variables for Agent Proxy](user-guide/deploy-install/advanced-options/agent-proxy/configure-custom-env-vars.md). For the dynamic claims expression syntax, see [OIDC and JWT-SVID dynamic claims](user-guide/access-policies/credential-providers/advanced-options/dynamic-claims-oidc.md#environment-variables).

### Edge components release with Oracle GA and HTTP proxy support

Aembit has released new versions of the following components and packages:

* Helm Chart
* Terraform ECS module
* VM Agent Proxy package
* Agent CLI
* AWS Lambda Extension
* AWS Lambda Layer
* Agent Injector
* Agent Proxy

For the latest available versions of these components, see the [Edge Components Supported Versions](reference/edge-components/edge-component-supported-versions.md) page.

Key Updates:

* **Oracle Application Protocol GA**: [Oracle Database protocol support](user-guide/access-policies/server-workloads/guides/oracle-database.md) is now available for production use, including mid-connection TLS support, improved client error handling, Prometheus metrics for Oracle credential injection events, and internal packet-handling improvements.
* **Upstream HTTP proxy support**: Agent Proxy and Aembit CLI now support [upstream HTTP proxy configuration](reference/edge-components/edge-component-env-vars.md#agent-proxy-environment-variables) for gRPC and Server-Workload-bound HTTP/HTTPS traffic, with `NO_PROXY` honored.
* **S3 upload size restriction removed**: Large file uploads to [AWS S3 Log Streams](user-guide/administration/log-streams/aws-s3.md) are now supported via streaming AWS chunked signing, removing the previous upload size limit. See [How Aembit uses AWS SigV4 and SigV4a](user-guide/access-policies/credential-providers/aws-sigv4.md) for more details.
* **Expanded credential resolver capabilities**: Enhanced support for credential provider resolution across deployment types.
* **Dynamic claims from environment variables**: Agent Proxy and Aembit CLI can now gather [dynamic claims from environment variables](user-guide/deploy-install/advanced-options/agent-proxy/configure-custom-env-vars.md), controlled by the `AEMBIT_ENV_VAR_ALLOWLIST`.
* **CLI enhancements**: Aembit CLI adds the [`--client-workload-id`](dev-guide/cli/reference/credentials-get.md) flag and OIDC token expiration validation.
* **General improvements**: Numerous stability reliability improvements across edge components.
* **Security upgrades**: Security dependency upgrades across edge components.
* **Improved logging and observability**: Improved request logging and enhanced error reporting for common failure conditions.

### Oracle Database now generally available

Oracle Database protocol support is now available for production use.

**What’s new:**

* **Oracle Database GA**: Support for Oracle 19c and 21c is now available for production use. Aembit injects username/password credentials into Oracle TNS connections at authentication time, eliminating static database passwords without modifying your application code.
* **TLS connections**: Oracle database connections can now use TLS via the TCP/IP with TLS (TCPS) protocol. You can enable TLS independently on the client-to-proxy and proxy-to-database sides by checking the **TLS** checkbox on the **Port** and **Forward to Port** fields in the Server Workload configuration.
* **Improved Oracle error handling**: Agent Proxy now returns clearer ORA-\* error messages when Oracle authentication fails, making it easier to diagnose credential injection and configuration issues.
* **Prometheus observability**: Oracle credential injection events now appear in the `aembit_agent_proxy_credential_injections_total` metric with `application_protocol="oracleDatabase"`, so you can monitor Oracle credential operations alongside other supported protocols.

For setup instructions, see [Create an Oracle Database Server Workload](user-guide/access-policies/server-workloads/guides/oracle-database.md). For a technical overview, see [About Oracle Databases](user-guide/deploy-install/databases/about-oracle-databases.md).

## 2026-04-28

### OAuth 2.0 Authorization Code now uses centralized callback URL

The OAuth 2.0 Authorization Code Credential Provider now uses a centralized callback URL and supports an optional Final Redirect URL that supports custom or embedded integration scenarios.

**What’s new:**

* **Centralized Callback URL** - OAuth 2.0 Authorization Code Credential Providers now use a single, centralized callback URL shared across Credential Providers on your Aembit stack. If you previously registered a per-tenant callback URL with a third-party provider, you don’t need to take any action.
* **Final Redirect URL** - A new optional field that redirects users to a specified URL after completing the OAuth authorization flow, instead of returning to the Aembit Credential Provider page. Contact [Aembit support](https://aembit.io/contact) to enable this feature.

For details, see [OAuth 2.0 Authorization Code Credential Provider](user-guide/access-policies/credential-providers/oauth-authorization-code.md).

## 2026-04-21

### Faster Aembit-minted token generation

Aembit optimized how it generates tokens for Credential Providers and MCP authorization flows. This reduces latency and improves scalability, especially for MCP-based use cases.

## 2026-04-17

### Agent Proxy now honors HTTP proxy environment variables

Aembit has released new versions of the following components and packages:

* Helm Chart
* Terraform ECS module
* VM Agent Proxy package
* AWS Lambda Extension
* AWS Lambda Layer
* Agent Proxy

Agent Proxy now honors `HTTP_PROXY`, `HTTPS_PROXY`, and `NO_PROXY` environment variables. If your network routes outbound traffic through an HTTP proxy, you can configure these environment variables so that Agent Proxy routes its outbound connections through the proxy.

For details, see [Agent Proxy environment variables](reference/edge-components/edge-component-env-vars.md#agent-proxy-environment-variables).

For the latest available versions of these components, see the [Edge Components Supported Versions](reference/edge-components/edge-component-supported-versions.md) page.

## 2026-04-15

### Agent Controller now honors HTTP proxy environment variables

Aembit has released Agent Controller version 1.30.3384.

Agent Controller now honors `HTTP_PROXY`, `HTTPS_PROXY`, and `NO_PROXY` environment variables. If your network routes outbound traffic through an HTTP proxy, you can configure these environment variables so that Agent Controller routes its outbound connections through the proxy.

For details, see [Agent Controller environment variables](reference/edge-components/edge-component-env-vars.md#agent-controller-environment-variables).

## 2026-04-07

### Refresh token support for MCP authorization flows

OIDC ID Token and Aembit Access Token Credential Providers now support refresh tokens for [MCP Authorization Server](user-guide/deploy-install/mcp-authorization-server/overview.md) flows. This feature applies exclusively to MCP Authorization Server use cases.

**What’s new:**

* An **Enable Refresh Token Support** option on OIDC ID Token and Aembit Access Token Credential Providers.
* An **Absolute Token Lifetime** setting that controls how long refresh tokens remain valid for exchanging for new access tokens after initial issuance.
* Refresh tokens are single-use. Each exchange returns a new refresh token.

When enabled, the MCP Authorization Server returns refresh tokens alongside access tokens during OAuth token requests. MCP clients can exchange a refresh token for a new access token and a new refresh token, maintaining an active session without completing a new authorization flow. This setting doesn’t affect other credential flows, such as Agent Proxy.

**To use this feature**, edit your Credential Provider, toggle **Enable Refresh Token Support** to on, and set the **Absolute Token Lifetime**.

For details, see [Token refresh](user-guide/deploy-install/mcp-authorization-server/concepts-mcp-auth-server.md#token-refresh), [OIDC ID Token](user-guide/access-policies/credential-providers/oidc-id-token.md), and [Aembit Access Token](user-guide/access-policies/credential-providers/aembit-access-token.md).

## 2026-04-03

### MCP Identity Gateway 1.30 release

Aembit has released [MCP Identity Gateway](user-guide/deploy-install/mcp-identity-gateway/overview.md) version 1.30.4549.

For the latest available versions of these components, see the [Edge Components Supported Versions](reference/edge-components/edge-component-supported-versions.md) page.

Key Updates:

* The Gateway now [authenticates requests before proxying](user-guide/deploy-install/mcp-identity-gateway/concepts-mcp-gateway.md#security-model) them to upstream MCP servers (new default behavior)
* MCP responses include [tool annotations](user-guide/deploy-install/mcp-identity-gateway/reference-mcp-gateway.md#tool-methods)
* The Gateway returns HTTP 405 for GET requests to the MCP endpoint
* Unauthorized (401) responses now include additional metadata for easier troubleshooting
* The Gateway forwards errors from upstream MCP servers to MCP clients
* The Gateway honors the [`AEMBIT_TRUSTED_ISSUER_DOMAINS`](user-guide/deploy-install/mcp-identity-gateway/env-vars-mcp-gateway.md#aembit_trusted_issuer_domains) environment variable for trusted issuer configuration
* A new [metrics endpoint](user-guide/deploy-install/mcp-identity-gateway/env-vars-mcp-gateway.md#aembit_metrics_port) provides Gateway operational metrics on a configurable port
* Improved compatibility with Claude Desktop and other MCP clients
* Improved handling of MCP servers that don’t support [resources](user-guide/deploy-install/mcp-identity-gateway/concepts-mcp-gateway.md#mcp-resource-support)
* General improvements to session management, installer reliability, and internal performance

## 2026-03-31

### MCP Authorization Server now supports unauthenticated flows

Aembit’s MCP Authorization Server now supports OAuth flows that don’t require end-user authentication. This enables use cases like ChatGPT apps and other MCP integrations where user sign-in isn’t needed or desired.

**What’s new:**

* An **Enforce SSO** option on Client Workloads with the [Redirect URI](user-guide/access-policies/client-workloads/identification/redirect-uri.md) identifier type. Enforce SSO is on by default, preserving the current behavior of requiring user authentication.
* When **Enforce SSO is on**, a multi-select dropdown lets you choose which SSO identity providers appear on the MCP authentication page. By default, the dropdown selects all configured identity providers.
* When **Enforce SSO is off**, the MCP Authorization Server issues access tokens without redirecting users to an identity provider. The flow needs no Trust Provider, but a Credential Provider is still required.
* Access Policies still apply as an authorization control. You can turn off policies or entities to block token issuance.

**To use this feature**, edit your Client Workload, select the Redirect URI client identifier, and configure **Enforce SSO** under **MCP Authorization Configuration**.

For details, see [Authentication support](user-guide/deploy-install/mcp-authorization-server/overview.md#authentication-support) and [MCP Authorization Server architecture](user-guide/deploy-install/mcp-authorization-server/concepts-mcp-auth-server.md#mcp-authorization-server-architecture).

## 2026-03-17

### MCP Identity Gateway now supports MCP resources

Aembit has released MCP Identity Gateway version 1.29.4419.

Key Updates:

* MCP resource support for the Identity Gateway

***

The MCP Identity Gateway now proxies **MCP resource requests** alongside tool requests. MCP servers that expose resources, such as files, database schemas, or application data, are now accessible through the Gateway. The same identity-aware access policies, credential isolation, and audit logging that govern tool invocations apply.

**What’s new:**

* **`resources/list`** discovers available resources across all assigned MCP servers. The Gateway fans out the request and aggregates results from all connected servers.
* **`resources/read`** retrieves a specific resource by URI from the appropriate MCP server.

**No action required.** Resource support is available automatically after upgrading to MCP Identity Gateway 1.29.4419. Your existing access policies, Trust Providers, and Credential Providers apply to resource requests with no configuration changes.

For details, see [MCP resource support](user-guide/deploy-install/mcp-identity-gateway/concepts-mcp-gateway.md#mcp-resource-support).

## 2026-03-11

### Edge components release with S3 stability and OpenShift improvements

Aembit has released new versions of the following components and packages:

* Helm Chart
* Terraform ECS module
* VM Agent Proxy package
* VM Agent Controller package
* Agent Proxy
* Agent Controller

For the latest available versions of these components, see the [Edge Components Supported Versions](reference/edge-components/edge-component-supported-versions.md) page.

Key Updates:

* Apply stability improvements for S3 uploads and downloads
* Improve Helm Chart compatibility across Kubernetes platforms including Red Hat OpenShift (ROSA)

## 2026-02-26

### Oracle Database support enters beta with new process-based identifiers

Aembit has released new versions of the following components and packages:

* Helm Chart
* VM Agent Proxy package
* VM Agent Controller package
* AWS Lambda Extension
* AWS Lambda Layer
* Agent Injector
* Agent Proxy
* Agent Controller

For the latest available versions of these components, see the [Edge Components Supported Versions](reference/edge-components/edge-component-supported-versions.md) page.

Key Updates:

* Oracle Database protocol support (Limited Beta)
* Support Process Command Line and Process Path client workload identification

***

Aembit’s Agent Proxy now supports the **Oracle Database** application protocol in Limited Beta. This enables Aembit to manage access for client workloads connecting to Oracle databases by intercepting the TNS wire protocol and injecting credentials transparently.

**Key capabilities:**

* Username/password credential injection for Oracle 19c and 21c databases (12C password verifier only)
* Support for thin Oracle clients (Java, Python), with experimental thick client support
* Tested with AWS RDS for Oracle and containerized Oracle environments
* Transparent steering on Linux VM deployments

For setup instructions, see the [Oracle Database Server Workload guide](user-guide/access-policies/server-workloads/guides/oracle-database.md). For an overview of how Oracle protocol support works, see [About Oracle Databases](user-guide/deploy-install/databases/about-oracle-databases.md).

***

Aembit’s Agent Proxy now supports **Process Command Line** and **Process Path** as Client Workload identifiers. These identifiers allow you to identify client workloads based on their full command line or executable path, providing more granular control over which applications can access your protected resources.

**Key capabilities:**

* **Process Command Line**: Identify workloads by the full command used to start them, including arguments. Supports wildcard matching to target specific arguments (for example, `*--env production*`).
* **Process Path**: Identify workloads by the exact filesystem path of the executable.
* Combine with other identifiers like Process Name and Process User Name for precise matching.
* Supports Linux virtual machine deployments.

For configuration details, see [Process Command Line](user-guide/access-policies/client-workloads/identification/process-command-line.md) and [Process Path](user-guide/access-policies/client-workloads/identification/process-path.md).

## 2026-02-24

### MCP Identity Gateway enters beta with MCP Server and component copying

Aembit now offers an **MCP Identity Gateway** (Beta) that sits between AI agents and MCP servers, enforcing Access Policies, performing secure token exchange, and providing visibility into MCP activity. Deployed on a Linux VM, the Gateway ensures AI agents never hold direct credentials for enterprise systems.

**Key capabilities:**

* Proxies MCP traffic with identity-aware policy enforcement
* Performs secure token exchange using OAuth 2.0 and API key credentials
* Provides per-user credential management and centralized MCP routing
* Logs agent identity, user identity, and policy decisions for auditability
* Fail-closed behavior—denies access by default unless explicitly allowed

For setup instructions and architecture details, see [MCP Identity Gateway](user-guide/deploy-install/mcp-identity-gateway/overview.md).

***

Aembit now provides an **MCP Server** that enables AI agents and users to query Aembit event logs using structured commands. Built on the Model Context Protocol specification, the MCP Server enables agentic observability and auditability for organizations using Aembit.

**Key capabilities:**

* Query audit logs, authorization events, and workload events
* Integrations with MCP Inspector, Claude Code, GitHub Copilot, and Visual Studio
* Resource-set-based access scoping for least-privilege access
* Read-only access—no create, update, or delete operations
* Full audit trail of all MCP Server queries

For setup and connection guides, see [Aembit MCP Server](user-guide/mcp-server/overview.md).

***

Aembit has added a new **MCP User-Based Access Token** Credential Provider type. This type enables per-user OAuth credentials for MCP servers using the OAuth 2.0 Authorization Code flow. The MCP Identity Gateway manages user-specific tokens when connecting to downstream MCP servers.

**Key capabilities:**

* OAuth 2.0 Authorization Code flow with Proof Key for Code Exchange (PKCE) support
* MCP Server URL discovery with auto-population of OAuth endpoints
* Per-user credential scoping
* Token introspection and lifetime management

For configuration details, see [MCP User-Based Access Token Credential Provider](user-guide/access-policies/credential-providers/mcp-user-based-access-token.md).

***

Aembit now supports **component copying** between Resource Sets. You can replicate Access Policy components—including Client Workloads, Server Workloads, Trust Providers, Credential Providers, and Access Conditions—from one Resource Set to another. You can also copy entire Access Policies with all related components at once.

**Key capabilities:**

* Copy individual components or entire Access Policies between Resource Sets
* Each copy receives a unique identifier while the original remains unchanged
* Supports environment promotion, regional deployments, and safe experimentation

For details, see [About component copying](user-guide/administration/resource-sets/about-component-copying.md) and [Copy components](user-guide/administration/resource-sets/copy-components.md).

## 2026-01-16

### Edge components release with S3 streaming and Secrets Manager improvements

Aembit has released new versions of the following components and packages:

* Helm Chart
* Terraform ECS module
* Agent Proxy

For the latest available versions of these components, see the [Edge Components Supported Versions](reference/edge-components/edge-component-supported-versions.md) page.

Key Updates:

* Improved AWS S3 upload streaming signature support
* AWS Secrets Manager Private Network Access username/password credential support (requires Agent Proxy 1.28)

***

Aembit has improved Agent Proxy’s AWS S3 upload support with enhanced streaming signature handling. Agent Proxy 1.28 addresses limitations from the 1.27 release related to streaming signed payloads.

**Key capabilities:**

* Improved handling of `aws-chunked` content encoding for streaming uploads
* Better compatibility with AWS SDK streaming operations
* Enhanced request signing for chunked transfer encoding

For complete documentation, see [How Aembit uses AWS SigV4 and SigV4a](user-guide/access-policies/credential-providers/aws-sigv4.md).

***

The AWS Secrets Manager Credential Provider with Private Network Access now supports username/password credentials. This extends the PNA capability introduced in Agent Proxy 1.27 to include secrets stored as username/password pairs.

**Requirements:**

* Agent Proxy 1.28 or later

For configuration details, see [AWS Secrets Manager Credential Provider](user-guide/access-policies/credential-providers/aws-secrets-manager.md).

* VM Agent Proxy package
* AWS Lambda Extension
* AWS Lambda Layer
* Agent Proxy

For the latest available versions of these components, please see the [Edge Components Supported Versions](reference/edge-components/edge-component-supported-versions.md) page.

Key Updates:

* Added [private network access](user-guide/access-policies/credential-providers/private-network-access.md) support for HTTP Basic Auth Credential Providers using AWS Secrets Manager.
* Added [process name](user-guide/access-policies/client-workloads/identification/process-name.md) and [process username](user-guide/access-policies/client-workloads/identification/process-user-name.md) as Client Workload Identifiers.
* Extended AWS S3 support to include all [SigV4](user-guide/access-policies/credential-providers/aws-sigv4.md) headers, enabling required signing type specification.

## 2026-01-13

### GitHub Action, MCP Authorization Server beta, and Access Policy Builder now available

Aembit now provides an official [GitHub Action](https://github.com/marketplace/actions/aembit-credentials) for injecting credentials into your CI/CD workflows. The action retrieves credentials from Aembit and makes them available to subsequent steps in your workflow.

**Key capabilities:**

* Retrieve credentials using workload identity federation with GitHub’s OIDC tokens
* Support for AWS, Azure, database, and API key credential types
* Automatic credential masking in workflow logs

For setup instructions, see the [GitHub Actions tutorial](user-guide/deploy-install/ci-cd/github/github-actions-tutorial.md). For usage examples with different credential types, see the [how-to guide](user-guide/deploy-install/ci-cd/github/github-actions-how-to.md).

***

Aembit now supports Private Network Access (PNA) for the AWS Secrets Manager Credential Provider. This allows your Aembit Edge components (Aembit CLI or Agent Proxy) to retrieve secrets directly from AWS Secrets Manager instances in private networks, such as AWS VPCs with private endpoints.

**Key capabilities:**

* Retrieve secrets from AWS Secrets Manager without exposing your VPC to the public internet
* Works with both Aembit CLI and Agent Proxy deployments
* No changes required to your existing AWS IAM policies or VPC endpoint configuration

For configuration details, see [Private Network Access for Credential Providers](user-guide/access-policies/credential-providers/private-network-access.md) and [AWS Secrets Manager Credential Provider](user-guide/access-policies/credential-providers/aws-secrets-manager.md#accessing-aws-secrets-manager-on-private-networks).

***

Aembit has released the MCP Authorization Server (beta), which secures Model Context Protocol (MCP) workloads using OAuth 2.1 authorization flows. This enables you to apply Aembit Access Policies to AI agents and MCP clients, controlling which users can access which MCP servers.

**Key capabilities:**

* OAuth 2.1 authorization code flow implementation for MCP-compliant workloads
* Dynamic Client Registration support for tools like Claude Desktop and Gemini CLI
* Integration with OIDC and SAML identity providers for user authentication
* Access Policies with time and location-based conditions

***

Aembit has redesigned the Access Policy creation experience with the new Access Policy Builder. The builder provides a card-based interface that guides you through configuring each component of an Access Policy.

![Access Policy Builder showing a completed policy configuration](https://docs.aembit.io/_astro/apb-policy-created.Lgg_r31Y_Z2m2jN2.webp)

**Key capabilities:**

* Visual card-based navigation for policy components
* Inline creation of Client Workloads, Server Workloads, Trust Providers, and other components
* Clear indicators for required, recommended, and optional components based on Global Policy Compliance settings

To use the new builder, enable **Use new access policy** in your user profile preferences. For a walkthrough, see [Create an Access Policy](user-guide/access-policies/create-access-policy.md).

## 2025-12-04

### Edge components release with AWS S3 uploads and multiple AWS STS support

Aembit has released new versions of the following components and packages:

* Helm Chart
* Terraform ECS module
* Agent Proxy
* AWS Lambda Extension
* AWS Lambda Layer

For the latest available versions of these components, see the [Edge Components Supported Versions](reference/edge-components/edge-component-supported-versions.md) page.

Key Updates:

* Support AWS S3 upload request workloads
* Support multiple AWS STS Credential Providers in a single Access Policy via Access Key ID mapping

***

Aembit’s Agent Proxy now supports AWS S3 file uploads. Agent Proxy transparently handles S3’s complex signing requirements, including detecting client signatures, re-signing requests with injected credentials, and streaming large file uploads.

**Key capabilities:**

* Automatic detection of S3 signing methods using the `x-amz-content-sha256` header
* Support for unsigned payloads, streaming signatures, and standard SigV4 signing
* Transparent credential injection without client-side configuration changes

**Known limitations in this release:**

* Streaming signed payload uploads default to a 50 MiB limit (configurable via [`AEMBIT_AWS_MAX_BUFFERED_PAYLOAD_BYTES`](reference/edge-components/edge-component-env-vars.md#aembit_aws_max_buffered_payload_bytes))
* Request compression isn’t supported for S3 requests

For complete documentation and workarounds, see [How Aembit uses AWS SigV4 and SigV4a](user-guide/access-policies/credential-providers/aws-sigv4.md).

***

Aembit now supports multiple AWS Security Token Service (STS) Credential Providers within a single Access Policy. This feature enables a single Client Workload to access multiple AWS resources, each requiring different IAM roles, without creating separate Access Policies.

**Key capabilities:**

* Access Key ID selectors for automatic Credential Provider matching
* Simplified policy management with multiple AWS STS Credential Providers per Access Policy
* Seamless credential injection for applications accessing different AWS services

**Minimum Edge Component versions required:**

* Agent Proxy 1.27.3865
* Agent Controller 1.27.2906

For complete documentation, see [Using multiple AWS STS Credential Providers](user-guide/access-policies/credential-providers/aws-security-token-service-multiple.md).

***

Aembit has expanded the Server Workload documentation with new guides covering architecture patterns, credential lifecycle management, developer integration, and troubleshooting. These resources help you understand how Aembit manages credentials for your Server Workloads and provide guidance for integrating Aembit into your applications.

**New documentation:**

* [Architecture patterns](user-guide/access-policies/server-workloads/architecture-patterns.md) - OAuth flows, trust boundaries, and deployment models
* [Credential lifecycle](user-guide/access-policies/server-workloads/credential-lifecycle.md) - How Aembit manages credential rotation and security
* [Developer integration](dev-guide/integration/agent-proxy.md) - SDK integration patterns and placeholder credentials for local development
* [Troubleshooting](user-guide/access-policies/server-workloads/troubleshooting.md) - Diagnostic steps for common configuration issues

**New and updated Server Workload guides:**

* NEW [Microsoft Entra ID](user-guide/access-policies/server-workloads/guides/entra-id.md) - Authenticate to Entra ID-protected resources using Azure Entra Workload Identity Federation or OAuth interception
* UPDATED [AWS services](user-guide/access-policies/server-workloads/guides/aws-cloud.md) - Authenticate to AWS services using AWS Security Token Service (STS) Credential Providers and SigV4 signing

## 2025-11-25

### Agent Controller logging and error handling improvements

Aembit has released new versions of the following components and packages:

* Helm Chart
* Terraform ECS module
* Agent Controller

For the latest available versions of these components, see the [Edge Components Supported Versions](reference/edge-components/edge-component-supported-versions.md) page.

Key Updates:

* Enhancements to Agent Controller logging and error handling for improved observability

***

## 2025-10-21

### Azure Key Vault Credential Provider and OIDC SSO now available

Aembit has released new versions of the following components and packages:

* Helm Chart
* Terraform ECS module
* Agent Proxy
* AWS Lambda Extension
* AWS Lambda Layer
* Agent Injector

For the latest available versions of these components, see the [Edge Components Supported Versions](reference/edge-components/edge-component-supported-versions.md) page.

Key Updates:

* **Azure Key Vault Private Network Access**: Added support for accessing Azure Key Vault instances configured with private network endpoints
* **Performance Improvements**: Enhanced performance for Secure Parameter Exchange (SPE) Postgres database operations
* **Dependency Updates**: Updated multiple project dependencies to their latest stable versions
* **Rust and Hyper Upgrade**: Upgraded to Rust 1.89.0 and introduced the hyper HTTP library for improved performance and security
* **Logging Enhancements**: Internal improvements to logging functionality for better observability and debugging

***

Aembit has released the new Azure Entra Federation Credential Provider Integration and Azure Key Vault Credential Provider.

Together, they enable you to retrieve secrets from Azure Key Vault directly through Aembit using Azure’s Workload Identity Federation.

The Azure Entra Federation integration leverages OpenID Connect (OIDC) standards to authenticate with Azure Entra without requiring long-lived secrets or static credentials. This allows Aembit to securely access your Azure Key Vault instances using short-lived, federated tokens.

The Azure Key Vault Credential Provider supports:

* Single value credentials (API keys, tokens)
* Username/Password credentials
* Both public and private network access scenarios
* Policy-driven access controls and centralized auditing

See [Azure Entra Federation Credential Provider Integration](user-guide/access-policies/credential-providers/integrations/azure-entra-federation.md) and [Azure Key Vault Credential Provider](user-guide/access-policies/credential-providers/azure-key-vault.md) to learn more.

***

You can now configure OIDC 1.0 Identity Providers for administrator Single Sign-On (SSO) authentication. This enables you to use OIDC-compliant identity providers such as Okta, Azure AD, and Auth0 to simplify the Aembit Tenant login process for your users. With OIDC support, you can leverage your existing identity infrastructure for secure, standardized authentication to the Aembit administrative console.

For more information, see [Create an OIDC Identity Provider](user-guide/administration/identity-providers/create-idp-oidc.md).

## 2025-10-02

### Edge components release with container base image security update

Aembit has released new versions of the following components and packages:

* Helm Chart
* Terraform ECS module
* AWS Lambda Extension
* AWS Lambda Layer
* Agent Proxy
* Agent Injector
* Sidecar Init

For the latest available versions of these components, see the [Edge Components Supported Versions](reference/edge-components/edge-component-supported-versions.md) page.

Key Updates:

* Applied a security enhancement to container-base images

## 2025-09-09

### Faster, more reliable Agent Controller cloud detection and attestation

Aembit has applied performance enhancements to Agent Controller in this release, including:

* improved cloud environment detection and attestation, making Agent Controller onboarding faster and more reliable across AWS and Azure
* improved logging around TLS-related errors
* deprecated the `AEMBIT_HTTP_DISABLED` environment variable (HTTP is now disabled when TLS is enabled)

For the latest available versions of these components, see the [Edge Components Supported Versions](reference/edge-components/edge-component-supported-versions.md) page.

## 2025-09-02

### New AWS Lambda layer and extension release

Aembit has released new versions of the following components and packages:

* AWS Lambda layer
* AWS Lambda extension

For the latest available versions of these components, see the [Edge Components Supported Versions](reference/edge-components/edge-component-supported-versions.md) page.

## 2025-08-26

### SPIFFE JWT-SVID Credential Provider now available

Aembit has added the SPIFFE JWT-SVID Credential Provider. This Credential Provider enables you to generate JWT-SVID tokens for workloads that require SPIFFE-compliant authentication and authorization.

See [About the SPIFFE JWT-SVID Credential Provider](user-guide/access-policies/credential-providers/about-spiffe-jwt-svid.md) and [JWT-SVID Token Credential Provider](user-guide/access-policies/credential-providers/spiffe-jwt-svid.md) for more information and configuration details.

## 2025-08-22

### Edge components release with OpenShift support and AWS Secrets Manager private network access

Aembit has updated Aembit Edge Components to include the latest versions of Agent Proxy, Sidecar Init, and the Aembit Helm chart. These updates include support for:

* Official Red Hat OpenShift and OpenShift Service on AWS (ROSA) support for Agent Proxy and Sidecar Init, including `SecurityContextConstraint` configurations and deployment best practices. See [OpenShift deployment guide](user-guide/deploy-install/kubernetes/openshift.md).
* AWS Secrets Manager private network access for Aembit CLI and Agent Proxy.
* Aembit CLI CrowdStrike support.
* Enhanced Helm chart with support for custom annotations on Kubernetes resources. See [Helm chart configuration options](reference/edge-components/helm-chart-config-options.md).
* New guide for managing Agent Injector TLS certificates in Kubernetes deployments. See [Managing Agent Injector certificates](user-guide/deploy-install/kubernetes/agent-injector-certificate.md).
* Support for volume-mounted certificates in Aembit Edge Components.
* Security and performance enhancements.

Updated Edge Components:

* Agent Proxy 1.25.3494
* Sidecar Init 1.25.127
* Helm Chart 1.25.494

See [Edge Components supported versions](reference/edge-components/edge-component-supported-versions.md) for more details.

***

Aembit has added Private Network Access to the AWS Secrets Manager Credential Provider. This feature allows you to securely access AWS Secrets Manager secrets from Aembit Edge Components running in private networks, such as AWS VPCs, without exposing them to the public internet.

When you enable Private Network Access, the Aembit CLI or Agent Proxy retrieve secrets from AWS Secrets Manager directly, ensuring secure and private access to your secrets.

See [AWS Secrets Manager Credential Provider](user-guide/access-policies/credential-providers/aws-secrets-manager.md#accessing-aws-secrets-manager-on-private-networks) for more details on how to configure this feature.

***

## 2025-08-19

### GitLab CI/CD Component, OIDC dynamic claims, and CrowdStrike conditions now available

The Aembit Edge GitLab CI/CD Component is now available to simplify Aembit integration within your pipelines. Find the component in the [GitLab CI/CD Catalog](https://gitlab.com/explore/catalog/aembit/aembit-edge) and learn how to use it in the [component documentation](user-guide/deploy-install/ci-cd/gitlab/gitlab-jobs-component.md).

***

The OIDC ID Token Credential Provider now supports dynamic claims, allowing you to extract and use values from OIDC tokens in the credential data. This feature creates personalized and context-aware credentials that reflect the workload’s identity and attributes from their original OIDC token.

See [OIDC ID Token Dynamic Claims](user-guide/access-policies/credential-providers/advanced-options/dynamic-claims-oidc.md) for more information.

***

Aembit has added two new Access Conditions for CrowdStrike:

* **MAC Address** - Ensures the CrowdStrike Agent Host MAC Address matches the Host MAC Address that Agent Proxy retrieved.
* **Local IP Address** - Ensures the CrowdStrike Agent Host Local IP Address matches the Host Local IP Address that Agent Proxy retrieved.

See [Create Access Conditions for CrowdStrike](user-guide/access-policies/access-conditions/crowdstrike.md) to learn how to create Access Conditions for CrowdStrike.

## 2025-07-29

### Aembit CLI, AWS Secrets Manager, and Jenkins Pipelines now available

Aembit has released the new AWS IAM Role Credential Provider Integration and Secrets Manager Credential Provider. Together, they enable you to retrieve secrets from AWS Secrets Manager directly through Aembit.

See [AWS IAM Role Credential Provider Integration](user-guide/access-policies/credential-providers/integrations/aws-iam-role.md) and [AWS Secrets Manager Credential Provider](user-guide/access-policies/credential-providers/aws-secrets-manager.md) to learn more.

***

Aembit has released the **Aembit CLI**, a command-line interface that allows you to inject credentials into your CI/CD pipelines. Compatible with GitLab, GitHub, and now Jenkins.

Check out the [Aembit CLI Guide](dev-guide/cli/overview.md) to get started with the Aembit CLI!\
Also, see [Aembit Edge on CI/CD services](user-guide/deploy-install/ci-cd/overview.md) for more information on how to use Aembit CLI with your CI/CD pipelines.

***

Aembit has released support for Jenkins Pipelines to help you integrate Aembit into your Jenkins CI/CD workflows. This integration allows you to securely retrieve and use Aembit-managed credentials directly in your Jenkins Pipelines, streamlining your CI/CD processes and enhancing security.

Check out [Jenkins Pipelines](user-guide/deploy-install/ci-cd/jenkins-pipelines.md) to learn more about how to use Aembit with Jenkins Pipelines.

***

Aembit now supports Server Workloads with a [wildcard hostname](user-guide/access-policies/server-workloads/overview.md#using-wildcard-domains).

This enables you to simplify your server workloads in a flexible and well defined manner.

***

As of Agent Controller version 1.24.xxxx, Aembit has enhanced Agent Controller to automatically close insecure HTTP ports when you enable TLS. This update streamlines security by ensuring only encrypted connections are active.

When you enable TLS, Agent Controller now automatically:

* Opens Secure Ports: 443 (or 5443 on VMs) and the secure Prometheus port 9091.
* Closes Insecure Ports: 80 (or 5000 on VMs) and the insecure Prometheus port 9090.

This automation removes the manual step of closing insecure, vulnerable ports, preventing potential misconfigurations and enforcing a more secure, “secure-by-default” posture.

***

Aembit has applied security enhancements to Agent Controller version `1.24.2485` in this release, including:

* Disabling insecure HTTP ports when you enable TLS.

Updated Edge Components:

* Agent Controller

Updated Edge Packages:

* Helm Chart

* Terraform ECS module

See [Edge Components supported versions](reference/edge-components/edge-component-supported-versions.md) for more details.

## 2025-07-22

### Discovery filtering and OIDC ID Token Trust Provider now available

Aembit has added more advanced filtering options to the **Discovered** tab for Client and Server Workloads. This enables you to find specific discovered workloads based on the criteria you filter.

![Discovered Client Workloads page](https://docs.aembit.io/_astro/discovery-client-workloads.ByRAoMa__Z7MR88.webp)

![Discovered Server Workloads page](https://docs.aembit.io/_astro/discovery-server-workloads.CO6lWVls_ejY2X.webp)

See [Filtering Discovered Workloads](user-guide/discovery/managing-discovered-workloads.md#filtering-discovered-workloads) for more info.

***

Aembit has added the OIDC ID Token Trust Provider. This Trust Provider is Aembit’s solution for authenticating workloads using standard OIDC ID tokens. It validates incoming tokens against specific issuer, audience, and subject claims, giving you maximum flexibility to integrate with virtually any OIDC-compliant identity provider for secure, token-based workload access.

See [OIDC ID Token Trust Provider](user-guide/access-policies/trust-providers/oidc-id-token-trust-provider.md) for more info.

***

Aembit has applied security and performance enhancements to Agent Proxy version `1.24.3324` in this release.

Updated Edge Components:

* Agent Proxy

Updated Edge Packages:

* Helm Chart

* Terraform ECS module

* AWS Lambda Extension

See [Edge Components supported versions](reference/edge-components/edge-component-supported-versions.md) for more details.

## 2025-06-24

### Aembit container images are now cryptographically signed

Aembit now cryptographically signs all container images in [Aembit’s Docker Hub repositories](https://hub.docker.com/u/aembit). See [Verifying Aembit container image signatures](user-guide/deploy-install/verify-container-images.md) for more details and how to verify Aembit container images.

## 2025-06-17

### Aembit Edge API now available with expanded Wiz Discovery

Introducing Aembit Edge API, the new way your cloud-native applications can retrieve credentials dynamically without deploying additional infrastructure. Perfect for serverless functions, containers, and CI/CD pipelines that need secure access to third-party services.

With Aembit Edge API you can:

* **Retrieve credentials on-demand** for any configured service from your CI/CD pipelines.
* **Authenticate workloads** using platform-native identity tokens (GitHub Actions, GitLab CI, AWS Lambda, etc.).
* **Remove hardcoded secrets** by fetching credentials just-in-time.
* **Support multiple credential types** including API keys, username/password, and CI/CD provider tokens.

Check out the [Edge API get started page](dev-guide/api/edge/overview.md) to learn more or start using it right away with the [Aembit Edge quickstart guide](dev-guide/api/edge/quickstart-edge.md).

***

Aembit Discovery can now discover additional resources when you use Wiz as a Discovery Source.

Through the Wiz integration, Aembit now discovers Client Workload resources such as VMs, AWS- and Azure-specific Client Workload Identifies, and many others. As for Server Workload resources, Aembit now discovers Azure Blob Storage, GCP BigQuery, and many others.

For the full list, see [Wiz-discoverable resource types](user-guide/discovery/sources/wiz.md#wiz-discoverable-resource-types).

## 2025-06-11

### Improved Agent Controller TLS reporting and environment variable logging

Aembit has released a new version of Agent Controller, version `1.23.2263`, with the following changes:

* Enhanced TLS certificate status reporting with improved retry and error handling.

* Added comprehensive logging for environment variable configuration with sensitive data masking for secure review.

Updated Edge Components:

* Agent Controller

Updated Edge Packages:

* Helm Chart

* VM Agent Controller package

* Terraform ECS module

See [Edge Components supported versions](reference/edge-components/edge-component-supported-versions.md) for more details.

## 2025-06-03

### Workload Discovery filtering and Global Policy Compliance reporting now available

Introducing **Workload Discovery Filtering** for improved workload management and visibility across your discovered infrastructure. This enhancement adds comprehensive filtering capabilities to both Client Workloads and Server Workloads discovery pages, enabling you to quickly locate and analyze specific workloads.

Filtering options include:

* **Client Workloads**: Filter by Client Workload Identifiers and Workload Discovery Source
* **Server Workloads**: Filter by Port, Protocol, and Workload Discovery Source

![Server Workload discovery filtering](https://docs.aembit.io/_astro/discovery-filtering-server-workloads.DYgQ_RTL_Z7jji8.webp)

This feature streamlines workload management by enabling you to efficiently search through discovered workloads, making it easier to identify, analyze, and onboard relevant workloads into your Aembit environment.

To learn more about discovered workload filtering, see [Workload Discovery Filtering](user-guide/discovery/managing-discovered-workloads.md#filtering-discovered-workloads).

***

You can now view the Global Policy Compliance status of your Access Policies using the new **Global Policy Compliance** page under **Reporting** in the left nav menu. Quickly get an overall view of the compliance status of your Access Policies and optionally filter for specific statuses.

![Global Policy Compliance report dashboard](https://docs.aembit.io/_astro/global-policy-compliance-report-dashboard.BybJxw5m_ZEGaSV.webp)

To learn more about reporting on Global Policy Compliance status, see [How to review Global Policy Compliance](user-guide/audit-report/global-policy.md).

## 2025-06-02

### Kerberos and PKI security enhancements for Agent Proxy

Aembit has released a new version of Agent Controller, version `1.23.2160`, with the following changes:

* Security enhancements for Kerberos and Aembit-managed PKI.

* Added the `AEMBIT_HTTP_PORT_DISABLED` environment variable to enable you to disable Agent Controller’s HTTP port.

***

Updated Edge Components:

* Agent Proxy 1.23.2160

Updated Edge Packages:

* Helm Chart 1.23

* Terraform ECS module 1.23

See [Edge Components supported versions](reference/edge-components/edge-component-supported-versions.md) for more details.

## 2025-05-30

### Improved Agent Injector pod security and TLS handling

Aembit has added enhancements to Agent Injector which include:

* Improved compatibility with pod-level `securityContext` settings for Kubernetes Client Workloads.
* TLS certificate security enhancements.
* Agent Injector now supports the [`AEMBIT_LOG_LEVEL`](reference/edge-components/edge-component-env-vars.md) environment variable.

## 2025-05-26

### CrowdStrike SIEM Log Streams and Agent Proxy enhancements

Introducing **Log Streams for CrowdStrike Next-Gen SIEM** for real-time security event monitoring and enhanced threat detection. This integration enables rapid streaming of Aembit Edge event logs and audit logs directly to CrowdStrike’s Next-Gen Security Information and Event Management (SIEM) platform using the HTTP Event Collector (HEC) protocol.

By connecting Aembit with CrowdStrike Next-Gen SIEM, you can:

* Stream Access Authorization Events, Audit Logs, and Workload Events to CrowdStrike SIEM
* Configure TLS encryption and verification options
* Automatic failure notifications for Aembit admins
* Seamless integration with existing CrowdStrike HEC configurations

This feature enhances your organization’s security posture by improving threat detection capabilities, streamlining incident management, and supporting compliance monitoring requirements through centralized log analysis in CrowdStrike.

To learn more, see [Log Streams for CrowdStrike Next-Gen SIEM](user-guide/administration/log-streams/crowdstrike-siem.md).

***

Aembit has applied security and performance enhancements to Agent Proxy in this release.

***

Aembit has added the `AEMBIT_CLIENT_WORKLOAD_PROCESS_IDENTIFICATION_ENABLED` Agent Proxy environment variable to Enable [Process Name](user-guide/access-policies/client-workloads/identification/process-name.md) Client Workload identification.

***

Updated Edge Components:

* Agent Proxy

Updated Edge Packages:

* Helm Chart

* VM Agent Proxy package

* Terraform ECS module

* AWS Lambda Extension

* AWS Lambda Layer

See [Edge Components supported versions](reference/edge-components/edge-component-supported-versions.md) for more details.

## 2025-05-22

### Terraform ECS module now supports environment variables

The Aembit Edge Terraform ECS module now supports Terraform variables that allow you to set Agent Controller and Agent Proxy [environment variables](reference/edge-components/edge-component-env-vars.md) directly.

You may now set logging levels for these Edge Components in AWS ECS Fargate environments, and leverage configuration options that the Edge Terraform ECS module doesn’t support directly as variables yet.

See [AWS ECS Fargate](user-guide/deploy-install/serverless/aws-ecs-fargate.md#configuration-variables) documentation for more information..

## 2025-05-06

### Global Policy Compliance, OIDC ID Token Credential Provider, and Splunk Log Streams now available

To increase the available deployment options for Amazon Web Services (AWS) Lambda users, Aembit now provides a Lambda Layer to support zip-based Lambda Functions. This joins our existing [AWS Lambda Container](user-guide/deploy-install/serverless/aws-lambda-container.md) support.

For more detailed information on how to deploy Aembit Edge Components to AWS Lambda Functions using our Lambda Layer, please refer to the [AWS Lambda Functions](user-guide/deploy-install/serverless/aws-lambda-function.md) documentation.

***

Introducing **Global Policy Compliance** for centralized security enforcement across your Aembit environment. This feature allows administrators to establish organization-wide security standards for Access Policies and Agent Controllers, ensuring consistent security practices and preventing the creation of policies that might inadvertently expose resources.

With Global Policy Compliance, you can enforce requirements for Trust Providers and Access Conditions across all Access Policies, as well as Trust Provider and TLS Hostname requirements for Agent Controllers. The three-tier enforcement model lets you set requirements as Required, Recommended (default), or Optional based on your organization’s security needs.

Global Policy Compliance visually identifies non-compliant components through color-coded status icons:

* Red indicators for required but missing elements
* Yellow indicators for recommended but missing elements
* Green indicators for compliant Access Policies
* Gray indicators for disabled or not active Access Policies

To learn more about Global Policy Compliance, see the [Global Policy Compliance Overview](user-guide/administration/global-policy/overview.md).

***

Introducing **OIDC ID Token Credential Provider** for secure identity token generation and exchange with third-party services. By leveraging Aembit’s custom Identity Provider (IdP) capabilities, this Credential Provider generates JWT-formatted tokens that seamlessly integrate with various Workload Identity Federation (WIF) solutions.

The OIDC ID Token Credential Provider offers flexible configuration options including:

* Custom claims configuration with both dynamic and literal subject support
* Choice of signing algorithms (RS256 or ES256)
* Integration with identity brokers such as AWS STS, GCP WIF, Azure WIF, and HashiCorp Vault

This new Credential Provider is particularly valuable for:

* Secure access to cloud provider resources through their WIF solutions
* Authentication with HashiCorp Vault using OIDC tokens
* Integration with any service supporting OIDC/JWT authentication

To learn more about this feature, see [About the OIDC ID Token Credential Provider](user-guide/access-policies/credential-providers/about-oidc-id-token.md).

***

Introducing **Log Stream for Splunk SIEM** to enhance your security monitoring capabilities. This integration enables rapid streaming of Aembit Edge event logs and audit logs directly to Splunk using Splunk’s HTTP Event Collector (HEC) protocol.

By connecting Aembit with Splunk SIEM, you can:

* Enhance threat detection with comprehensive security data
* Improve incident management through centralized logging
* Streamline compliance monitoring for your organization

The setup process is straightforward, requiring only a properly configured HTTP Event Collector in your Splunk environment and a few configuration steps in the Aembit Admin UI. Aembit will automatically send email notifications if Log Stream transactions consistently fail, ensuring you’re always aware of your logging status.

To learn more about setting up this integration, see [How to stream Aembit events to Splunk SIEM](user-guide/administration/log-streams/splunk-siem.md).

## 2025-04-25

### Workload Discovery now available

Aembit has released the new [Discovery](user-guide/discovery/overview.md) feature, which automatically identifies workloads across your infrastructure, increasing the visibility, scalability, and access control over your workloads.

Discovery uses [Sources](user-guide/discovery/sources/overview.md) to find workloads in your environments, natively through [Aembit Edge Discovery](user-guide/discovery/sources/aembit-edge.md) and through integrations with services such as [Wiz](user-guide/discovery/sources/wiz.md).

See [Discovery](user-guide/discovery/overview.md) for full details.

## 2025-04-24

### Aembit Edge now available on AWS EKS Fargate

Aembit now supports deploying Edge Components on AWS Elastic Kubernetes Service (EKS) using Fargate compute profiles. For details on feature support in this environment, please refer to Aembit’s [AWS EKS Fargate deployment guide](user-guide/deploy-install/kubernetes/aws-eks-fargate.md) and product [support matrix](reference/support-matrix.md).

## 2025-04-22

### GitLab.com support and service account naming for Managed GitLab Account Credential Provider

For the [GitLab Managed Service Account](user-guide/access-policies/credential-providers/managed-gitlab-account.md) Credential Provider, you can now specify the name of the service account that Aembit creates in GitLab for that Credential Provider.

Additionally, you can now create GitLab Service Account integrations for GitLab.com plans. See [Create a GitLab Service Account Integration for a GitLab.com plan](user-guide/access-policies/credential-providers/integrations/gitlab.md)

## 2025-04-21

### Pod startup delay and security enhancements for Agent Proxy

Aembit has added the `AEMBIT_PASS_THROUGH_TRAFFIC_BEFORE_REGISTRATION` Agent Proxy environment variable to enable you to delay the Client Workload Kubernetes pod startup until registration between Agent Proxy and Agent Controller completes. See [Delaying pod startup until Agent Proxy has registered](user-guide/deploy-install/kubernetes/kubernetes.md#delaying-pod-startup-until-agent-proxy-has-registered) for details.

***

Aembit has applied security enhancements and hardening to Agent Proxy in this release.

***

Updated Edge Components:

* Agent Proxy

Updated Edge Packages:

* Helm Chart

* VM Agent Proxy package

* Terraform ECS module

* AWS Lambda Extension

See [Edge Components supported versions](reference/edge-components/edge-component-supported-versions.md) for more details.

## 2025-04-15

### Allowed TLS Hostname now configurable for Agent Controller

Agent Controllers now support **Allowed TLS Hostname** as a configurable field in your Aembit Tenant:

![Create an Agent Controller with TLS Hostname field](https://docs.aembit.io/_astro/edge-components-agent-controller-tls-hostname.DD3zsqAX_29Qu0R.webp)

**Allowed TLS Hostname** serves the same purpose as the [`AEMBIT_MANAGED_TLS_HOSTNAME`](reference/edge-components/edge-component-env-vars.md#agent-controller-environment-variables) Agent Controller environment variable.

Configuring an **Allowed TLS Hostname** allows you to specify which domain name Aembit Managed TLS includes in the TLS certificate. This makes sure secure connections from your Agent Proxies are only valid when using this exact domain name to reach your Agent Controller, enhancing security without restricting which Agent Proxies can communicate with it.

To configure your Agent Controller with an allowed TLS hostname, see [How to create and Agent Controller](user-guide/deploy-install/advanced-options/agent-controller/create-agent-controller.md) or [Configure Agent Controller TLS with Aembit’s PKI](user-guide/deploy-install/advanced-options/agent-controller/configure-aembit-pki-agent-controller-tls.md).

## 2025-04-03

### Windows Server now supported for Kerberos Trust Provider and Agent Controller

The Kerberos Trust Provider now supports the attestation of Client Workloads running on Windows Server virtual machines (VMs) joined to Active Directory (AD). See [Kerberos Trust Provider](user-guide/access-policies/trust-providers/kerberos-trust-provider.md) for details.

You can now install Agent Controller on Windows Server 2019 and Windows Server 2022 virtual machines. See [Agent Controller on Windows Server](user-guide/deploy-install/virtual-machine/windows/agent-controller-install-windows.md) for details.

## 2025-03-25

### Standalone CAs and Credential Provider Integrations now available

Introducing **Standalone CAs** for more granular control over TLS Decrypt management. This feature allows you to create and manage dedicated Certificate Authorities (CAs) that function independently from Aembit’s default Tenant-level certificates.

With Standalone CAs, you can assign CAs directly to specific Client Workloads or Resource Sets, creating isolated trust boundaries and enabling precise management of TLS traffic across different environments. Aembit intelligently selects the appropriate CA using a clear hierarchy: Client Workload level -> Resource Set level -> Tenant level.

To learn more about Standalone CAs, see [About Standalone CA for TLS Decrypt](user-guide/deploy-install/advanced-options/tls-decrypt/about-tls-decrypt-standalone-ca.md).

***

We’ve updated the **Deploy Edge Components** experience in the Aembit admin UI to streamline how you deploy Aembit Edge Components.

We’ve added deployment guides directly in the Aembit admin UI for each type of deployment such as Kubernetes, Ubuntu Linux, Red Hat Enterprise Linux, or Microsoft. Now when you’re deploying new Aembit Edge Components, you’ll have a guided experience to get you up and running faster.

![Deploy Aembit Edge screen](https://docs.aembit.io/_astro/deployment-model-showcase.D2JGe02q_24zQgO.webp)

***

Introducing [Credential Provider Integrations](user-guide/access-policies/credential-providers/overview.md), which automate credential lifecycle management for third-party systems. This feature makes sure your workloads always have valid credentials without manual management, enhancing both security and operational efficiency, eliminating manual credential management.

Our new Credential Provider Integrations feature makes this possible by connecting Aembit directly to third-party systems like with the [GitLab Service Account integration](user-guide/access-policies/credential-providers/integrations/gitlab.md). The GitLab Service Account integration enables you to create a [Managed GitLab Account Credential Provider](user-guide/access-policies/credential-providers/managed-gitlab-account.md), which allows you to manage the credential lifecycle of your GitLab service accounts.

This gives you fine-grained control while eliminating the overhead of manual credential management.

## 2025-03-05

### AWS SigV4 and SigV4a request signing now supported

The Aembit Credential Provider for AWS Security Token Service (STS) now supports the AWS SigV4 and SigV4a request signing protocols. Aembit automatically signs requests to AWS services using SigV4 for regional services or SigV4a for global/multi-region services.

See [How Aembit uses AWS SigV4 and SigV4a](user-guide/access-policies/credential-providers/aws-sigv4.md) to learn more and [AWS Security Token Service (STS) Federation](user-guide/access-policies/credential-providers/aws-security-token-service-federation.md) to configure an AWS STS Credential Provider.

Updated Edge Components:

* Agent Proxy

Updated Edge Packages:

* Helm Chart

* VM Agent Proxy package

* Terraform ECS module

* AWS Lambda Extension

See [Edge Components supported versions](reference/edge-components/edge-component-supported-versions.md).

## 2025-03-03

### Agent Proxy SIGTERM termination behavior restored

Restored Agent Proxy termination behavior when you set `AEMBIT_SIGTERM_STRATEGY` to `immediate`.

Updated Edge Components:

* Agent Proxy

Updated Edge Packages:

* Helm Chart

* VM Agent Proxy package

* AWS Lambda Extension

See [Edge Components supported versions](reference/edge-components/edge-component-supported-versions.md).

## 2025-02-27

### Agent Controller now serves the full CA certificate chain

Enhanced Agent Controllers to now serve the entire CA certificate chain instead of just the leaf certificate.

Updated Edge Components:

* Agent Controller

Updated Edge Packages:

* Helm Chart version

* Terraform ECS module version

* VM Agent Controller package

See [Edge Components supported versions](reference/edge-components/edge-component-supported-versions.md).

## 2025-02-25

### Wiz Access Conditions now support Lambda Containers

Aembit’s Access Condition integration with Wiz now supports Lambda Containers. See [Access Condition for Wiz](user-guide/access-policies/access-conditions/wiz.md) to configure an Access Condition.

## 2025-02-20

### Vault private network access and CrowdStrike on Windows now available

Aembit now supports accessing HashiCorp Vault Credential Providers that reside on private networks. This allows your colocated Agent Proxy to handle authentication directly instead of Aembit Cloud. See [Accessing Vault on private networks](user-guide/access-policies/credential-providers/vault-client-token.md#accessing-vault-on-private-networks) for more info.

Aembit now supports Conditional Access for CrowdStrike on Windows. To set up Conditional Access for CrowdStrike on Windows, follow the steps in [Access Condition for CrowdStrike](user-guide/access-policies/access-conditions/crowdstrike.md).

Aembit now supports the AWS Role Trust Provider on Agent Proxy for ECS Fargate deployments.

Enhanced Vault token header behavior.

Enhanced Agent Proxy initialization on Kubernetes to prevent other processes from interfering and impacting its startup.

Updated Edge Components:

* Agent Proxy

Updated Edge Packages:

* Helm Chart

* Terraform ECS module

* VM Agent Proxy package

* AWS Lambda Extension

See [Edge Components supported versions](reference/edge-components/edge-component-supported-versions.md).

## 2025-02-11

### Azure Entra Workload Identity Federation and automatic user creation now available

Aembit now supports[Azure Entra Workload Identity Federation as a Credential Provider](user-guide/access-policies/credential-providers/azure-entra-workload-identity-federation.md). This enables you to automatically obtain credentials through Aembit as a third-party federated Identity Provider (IdP) to securely authenticate with Azure Entra to access your Azure Entra registered applications and managed identities.

Aembit now supports [Automatic User Creation](user-guide/administration/identity-providers/automatic-user-creation.md) triggered by SSO login requests. Aembit has enhanced the Identity Provider configuration page with additional parameters, enabling you to map SAML attributes from your Identity Provider to the user roles defined in your Aembit Tenant.

You can now [change the leaf certificate lifetime](user-guide/deploy-install/advanced-options/tls-decrypt/configure-tls-decrypt.md#change-your-leaf-certificate-lifetime) when using the TLS Decrypt feature.

## 2025-01-28

### Agent Proxy now available on Windows Server virtual machines

Aembit Agent Proxy supports virtual machine deployments for Windows Server 2019 and Windows Server 2022. See [Agent Proxy install](user-guide/deploy-install/virtual-machine/linux/agent-proxy-install-linux.md) for details.

## 2024-12-26

### RHEL with SELinux now supported for Edge components

Aembit Edge Components have been updated to include support for RedHat Enterprise Linux (RHEL) 8 and 9 with Security-Enhanced Linux (SELinux). With this improvement, administrators may now add additional layers of security to their system architecture.

For more information on integrating Aembit Edge Components with SELinux, please see the [SELinux support](user-guide/deploy-install/virtual-machine/linux/agent-proxy-selinux-config.md) page.

## 2024-12-17

### SignOn Policy now available for custom login experiences

Aembit has added support for defining a SignOn Policy, enabling you to customize the login experience for your users.

For more information, please see the [SignOn Policy](user-guide/administration/sign-on-policy/overview.md) page.

## 2024-11-22

### Earlier Client Workload identification in AWS Lambda Extension

Aembit has released an updated AWS Lambda Extension, enhancing support for Client Workload identification earlier in the Lambda container lifecycle.

For more information, please refer to the [AWS Lambda Container Supported Phases](user-guide/deploy-install/serverless/aws-lambda-container.md#supported-phases).

## 2024-10-29

### Edge components release with performance improvements

Aembit Edge Components have been updated to newer versions to improve overall performance and functionality.

The following components and packages have been updated:

* Helm Chart
* Agent Proxy

For the latest available versions of these components, please see the [Edge Components Supported Versions](reference/edge-components/edge-component-supported-versions.md) page.

## 2024-10-28

### Network traffic tracing now available for Agent Proxy debugging

Agent Proxy has been updated to include a new environment variable that enables Agent Proxy to monitor network traffic so you can perform detailed debugging if you encounter network traffic errors.

For more detailed information on this feature, please see the [Agent Proxy Debug Network Tracing](user-guide/troubleshooting/agent-proxy-debug-network-tracing.md) page.

## 2024-10-25

### Multiple match rules of the same type now supported in Terraform Provider

The Aembit Terraform Provider is regularly updated with new features and capabilities to give you additional configuration options.

You may now use multiple Trust Provider match rules of the same type (OR-based combinations) in your Terraform Provider configuration.

For more detailed technical information on how to use similar match rule types in GitLab using the Aembit Terraform Provider, please see the [Aembit Terraform Provider Registry](https://registry.terraform.io/providers/Aembit/aembit/latest/docs) technical documentation.

## 2024-10-23

### Explicit steering and enhanced access authorization events now available

Aembit regularly releases new enhancements and improvements to Aembit Edge and Aembit Cloud components to provide additional features and functionality for your Aembit environment.

The following new features and enhancements have been released:

* Enhanced Access Authorization Events
* Explicit Steering
* Updated Aembit Edge Component Versions

#### Enhanced Access Authorization Events

Aembit automatically records and collects various types of workload metadata in access authorization events, enabling you to use this information to audit and analyze security events.

The information collected and recorded in these access authorization events has been enhanced to now capture and display additional workload metadata, including **VM hostname**, **IP address**, and **process name**.

For more information on access authorization events, please refer to the following technical documentation pages:

* [Access Authorization Events](user-guide/audit-report/access-authorization-events.md)

#### Explicit Steering

Aembit continues to look for ways to improve the overall user experience in an Aembit environment, while also providing additional functionality and features that enhance this experience. One of these ways is by enabling you to route only specific types of traffic through Aembit, via the explicit steering feature.

With explicit steering, you can now configure Client Workloads to direct only certain types of traffic to the Agent Proxy. This enables you to have more precise control of which traffic is managed by the Agent Proxy.

For more information on the explicit steering feature, please refer to the [Explicit Steering](user-guide/deploy-install/advanced-options/agent-proxy/explicit-steering.md) page.

#### Aembit Edge Components Update

Aembit Edge Components have been updated to newer versions to improve overall performance and functionality.

The following components and packages have been updated:

* Helm Chart
* Agent Controller
* Agent Proxy

For the latest available versions of these components, please see the [Edge Components Supported Versions](reference/edge-components/edge-component-supported-versions.md) page.

## 2024-10-18

### GitLab Jobs now supported in the Aembit Terraform Provider

The Aembit Terraform Provider is regularly updated with new features and capabilities to give you additional configuration options.

Aembit now supports both GitLab Job Client Identifiers and GitLab Job Trust Provider types, enabling you to manage Client Workloads in Gitlab using the Aembit Terraform Provider.

For more detailed technical information on how to manage Client Workloads in GitLab using the Aembit Terraform Provider, please see the [Aembit Terraform Provider Registry](https://registry.terraform.io/providers/Aembit/aembit/latest/docs) technical documentation.

## 2024-10-08

### Multi-Credential Provider Terraform support and Prometheus metrics now available

Aembit regularly releases new enhancements and improvements to Aembit Edge and Aembit Cloud components to provide additional features and functionality for your Aembit environment.

The following four new major features have been released:

* Terraform Provider support for Access Policies with Multiple Credential Providers
* Admin Dashboard enhancements and improvements
* Exposure of Prometheus-compatible Aembit Edge metrics
* Updated Edge Component Versions

#### Terraform Provider Support for Access Policies with Multiple Credential Providers

Aembit has released a Terraform Provider update that enables users to add multiple Credential Providers to an Access Policy.

Aembit now supports use cases where the Aembit Terraform Provider can manage Aembit Access Policies associated with individual or multiple Credential Providers.

For more information about this feature, please see the [Multiple Credential Providers - Terraform](user-guide/access-policies/credential-providers/advanced-options/multiple-credential-providers-terraform.md) page.

#### Admin Dashboard Enhancements and Improvements

Aembit continually makes improvements and enhancements to the Admin Dashboard to provide greater visibility and insight into your Aembit environment.

The Admin Dashboard has been updated and enhanced with additional tiles and panels that provide detailed information on Client and Server Workloads, Credential Usage by Type, the number of Access Condition failures based on Access Policies over the past 24 hours, and several other visualizations.

For more information on the Admin Dashboard and these additional panels, please see the [Admin Dashboard Overview](user-guide/administration/admin-dashboard/overview.md) page.

#### Exposure of Prometheus-compatible Aembit Edge Metrics

Aembit aims to provides users with the ability to view detailed Aembit Edge metrics and data.

Aembit now exposes Prometheus-compatible metrics which enables users to view, and troubleshoot Aembit Edge Components (Agent Proxy, Agent Controller, and Agent Injector), while supporting both Kubernetes and virtual machine deployment models.

For more detailed information on how Aembit exposes Prometheus-compatible metrics, please see the [Aembit Edge Prometheus-compatible Metrics](user-guide/deploy-install/advanced-options/aembit-edge-prometheus-compatible-metrics.md) page.

#### Aembit Edge Components Update

Aembit Edge Components have been updated to newer versions to improve overall performance and functionality.

The following components and packages have been updated:

* Helm Chart
* Terraform ECS Module
* AWS Lambda Extension
* VM Artifacts
* Agent Controller
* Agent Proxy

For the latest available versions of these components, please see the [Edge Components Supported Versions](reference/edge-components/edge-component-supported-versions.md) page.

## 2024-10-01

### Improved access authorization events and audit logging

Aembit has released improvements to its reporting and logging/auditing capabilities, giving you improved visibility into access authorization events and audit logs. With these enhancements, you can more easily diagnose issues and troubleshoot problems in your environment.

#### Improved Access Authorization Events and Audit Logging

Improvements have been made to the Aembit Tenant’s reporting capabilities and reporting documentation, enabling increased visibility into access authorization events and audit logs. The Aembit technical documentation has also been augmented to assist with using these capabilities.

For more information on these access authorization event and audit log improvements, please see the following pages:

* [Access Authorization Events](user-guide/audit-report/access-authorization-events.md)
* [Audit Logs](user-guide/audit-report/audit-logs.md)

## 2024-09-30

### Real-time Agent Controller health monitoring now available

Aembit has released two new updates and improvements to Aembit components:

* Agent Controller functionality has been enhanced to enable real-time monitoring and status of Agent Controllers in the Aembit Tenant.
* Aembit Edge Components and packages have been updated to the latest versions.

#### Agent Controller Real-Time Health Status and Health Update

You may now view the real-time health status of Agent Controllers in the Aembit Tenant.

For more information on how to check the health status of Agent Controllers, please see the [Tenant Health Check](user-guide/troubleshooting/tenant-health-check.md) page.

#### Edge Components Update

Aembit Edge Components have been updated to newer versions to improve overall performance and functionality.

The following components and packages have been updated:

* Helm Chart
* Terraform ECS Module
* VM Artifacts
* Agent Controller

For the latest available versions of these components, please see the [Edge Components Supported Versions](reference/edge-components/edge-component-supported-versions.md) page.

## 2024-09-20

### Helm Chart fix for Edge components

Aembit Edge Components are regularly updated to newer versions to address specific bug fixes and optimize performance of these components.

We recently identified a known issue that was resolved with a new Helm Chart version.

For the latest available versions of these components, please see the [Edge Components Supported Versions](reference/edge-components/edge-component-supported-versions.md) page.

## 2024-09-18

### Custom Resource Sets now supported for GitHub Actions and GitLab Jobs

Aembit regularly provides feature and functionality updates to various components to extend capabilities and performance.

Aembit has released a feature improvement that enables you to work with Custom Resource Sets in GitHub Actions and GitLab Jobs CI/CD pipelines.

#### Custom Resource Set Support for GitHub Actions and GitLab Jobs

For users that would like to implement a CI/CD pipeline solution using Aembit with a custom Resource Set, separate from other workloads, Aembit has introduced Resource Set support for both GitHub Actions and GitLab Jobs.

Aembit supports Workload Identity and Access with GitHub Actions or GitLab Jobs, in your CI/CD workloads and encourages scoping these for appropriate access control. Adding support for Resource Sets in these solutions provides you with additional options and flexibility in best managing and protecting your CI/CD workloads.

For more information on how to configure Resource Sets in GitHub Actions and GitLab Jobs, please see the following pages:

* [GitHub Actions](user-guide/deploy-install/ci-cd/github/overview.md)
* [GitLab Jobs](user-guide/deploy-install/ci-cd/gitlab/overview.md)

## 2024-09-17

### Aembit PKI Agent Controller TLS now available for Kubernetes and virtual machines

Aembit regularly releases updates to Aembit components and packages to improve overall performance of your environment.

The following updates have been released:

* Aembit Edge Component Updates
* Agent Controller PKI-Based TLS Support for Kubernetes and virtual machines

#### Aembit Edge Component Updates

Aembit Edge Components have been updated to newer versions to improve overall performance and functionality.

The following components and packages have been updated:

* Helm Chart
* Terraform ECS Module
* VM Artifacts
* AWS Lambda Extension

For the latest available versions of these components, please see the [Edge Components Supported Versions](reference/edge-components/edge-component-supported-versions.md) page.

#### Agent Controller PKI-Based TLS Support for Kubernetes and virtual machine Deployments

Aembit has extended the Aembit PKI-based Agent Controller TLS functionality beyond just ECS deployment models to include Kubernetes and virtual machine deployments.

* For Kubernetes deployments, if the Customer’s PKI-based Agent Controller is already configured, it will remain unchanged. Otherwise, Aembit’s PKI-based Agent Controller TLS is enabled by default.

* For virtual machine deployments, you need to configure Aembit’s PKI-based Agent Controller TLS manually.

## 2024-09-05

### Edge components release with Agent Proxy idle timeout fix

Aembit Edge Components are updated on a regular basis to include new features, functionality, and package improvements.

Aembit has released new versions of the following components and packages:

* Helm Chart
* Terraform ECS Module
* VM Artifacts
* AWS Lambda
* Agent Proxy

Agent Proxy has been updated to address a specific issue related to idle timeouts for HTTP persistent connections (currently 1 hour). If no new request comes over a connection, the request will be closed by Agent Proxy.

For the latest available versions of these components, please see the [Edge Components Supported Versions](reference/edge-components/edge-component-supported-versions.md) page.

## 2024-08-27

### Updated Admin Dashboard and multiple Credential Providers per Access Policy

Aembit recently released the following two updates to improve the Aembit user experience:

* The Aembit Tenant UI has been updated with an expanded Admin Dashboard with additional metrics and data.
* Access Policies have been improved to enable users to add multiple Credential Providers to Access Policies.

#### Updated Admin Dashboard

Aembit has released an updated Admin Dashboard with additional metrics and data you can review when logging into your tenant. You will now see the following metrics displayed from the last 24 hours:

* Client Workloads (Managed)
* Server Workloads (Managed)
* Credentials (Usage By Type)
* Workloads Connections (Managed)

#### Multiple Service Accounts per Access Policy

Aembit now supports the ability for you to have multiple Credential Providers associated with an Access Policy for specific use cases.

Adding and mapping multiple Credential Providers to an Access Policy can be very useful when you have a single Access Policy, but want to have different Credential Providers associated with that Access Policy.

For example, if you want to have the same Client Workload access the same Server Workload, but use different credentials for different functions, this feature enables you to specify the appropriate Credential Providers for each function on an Access Policy.

For more detailed information on how you can add multiple Credential Providers to an Access Policy, please see the [Multiple Credential Providers](user-guide/access-policies/credential-providers/multiple-credential-providers.md) page.

## 2024-08-09

### Agent Proxy now injected as a native Kubernetes sidecar

Kubernetes recently introduced support for native sidecar containers. Aembit now leverages this model for the Agent Proxy, where possible.

Aembit now automatically injects the Agent Proxy as a native sidecar, allowing init container Client Workloads.

This change only applies to Kubernetes deployments of version 1.29 and above.

For more information on how you can use Agent Proxy as a sidecar to support init containers, please see the [Kubernetes Deployment](user-guide/deploy-install/kubernetes/kubernetes.md#optional-configurations) page.

## 2024-08-01

### Comprehensive Aembit API documentation now available

Aembit has released comprehensive API technical documentation for the Aembit API.

With this documentation release, you now have access to a complete library technical content, usage information, and the latest version of the OpenAPI specification, which you can use to learn how to use the Aembit API.

For more detailed information on the Aembit API technical documentation, please see the page.

## 2024-07-29

### Aembit Edge Terraform module and ECS TLS support now available

Aembit has released two major enhancements to Aembit Edge Components: Aembit Edge Terraform Module for AWS ECS, and ECS TLS support.

#### Aembit ECS Terraform Registry

Aembit releases updates to the Aembit ECS Terraform Registry on a regular basis to provide users with additional features and functionality, including improvements to Agent Proxy and Agent Controller.

For more information on the latest ECS Terraform Registry release, please see the [Aembit Terraform Registry](https://registry.terraform.io/modules/Aembit/ecs/aembit/latest) page.

#### ECS TLS Support

Aembit has released an ECS deployment enhancement that enable Transport Layer Security (TLS) between the Agent Proxy and Agent Controller using Aembit-provided Private Key Infrastructure (PKI).

There is no option to use your own PKI for ECS deployments.

## 2024-07-18

### Aembit Terraform Provider update with Custom Resource Sets and OAuth

Aembit has released an Aembit Terraform Provider update to the Terraform Registry.

This update includes several improvements and enhancements, including:

* Support for Custom Resource Sets.
* Removal of the deprecated AWS ECS Role Trust Provider (replaced previously by the AWS Role Trust Provider).
* Support for Credential Providers of type OAuth2 Authorization Code.

For more information on these updates and changes, please see the [Aembit Terraform Registry](https://registry.terraform.io/providers/Aembit/aembit/1.15.0) page.

## 2024-07-03

### Dynamic steering to specific hostnames now available

Aembit now supports dynamically steering only specific traffic to the Agent Proxy.

The dynamic steering feature introduces the ability to restrict this proxied traffic to a specific list of hostnames. When this feature is enabled, only egress traffic to the user-specified hostnames will be proxied. This enables you to have more precise control over which destinations’ traffic is managed by the Agent Proxy.

## 2024-06-26

### Expanded Client Workload identification and Trust Provider match rules

Aembit now supports more options for identifying Client Workloads and specifying Trust Provider match rules, including multiple “or” condition matches and wildcard support.

These matching improvements shipped alongside the new [OAuth 2.0 Authorization Code Credential Provider](https://docs.aembit.io/changelog/entry/2024-06-26-oauth-2-0-authorization-code-credential-provider-now-available); see that entry for the new-feature details.

### OAuth 2.0 Authorization Code Credential Provider now available

Aembit now supports 3-legged OAuth (3LO) workflows through the new OAuth 2.0 Authorization Code Credential Provider. Applications can request a user’s permission to access their account data and act on the user’s behalf.

With 3LO support, an application can access services or applications that the user has authorized.

Aembit supports the following third-party services with OAuth 2.0 Authorization Code Credential Providers:

* [Atlassian](https://developer.atlassian.com/cloud/confluence/oauth-2-3lo-apps/)
* [GitLab](https://docs.gitlab.com/ee/api/oauth2.html)
* [Slack](https://api.slack.com/legacy/oauth)
* [GCP BigQuery](https://cloud.google.com/bigquery/docs/third-party-integration#authentication)
* [Apigee](https://docs.apigee.com/api-platform/security/oauth/oauth-introduction)
* [PagerDuty](https://www.pagerduty.com/blog/build-sophisticated-apps-for-your-pagerduty-environment-using-oauth-2-0-and-api-scopes/)

For configuration details, see the [OAuth 2.0 Authorization Code Credential](user-guide/access-policies/credential-providers/oauth-authorization-code.md) documentation.

An expansion to Client Workload identification and Trust Provider match rules also shipped in this release; see [Expanded Client Workload identification and Trust Provider match rules](https://docs.aembit.io/changelog/entry/2024-06-26-expanded-client-workload-identification-and-trust-provider-match-rules).

## 2024-06-10

### OAuth 2.0 Authorization Code Credential Provider enters beta

Aembit has released beta support for the OAuth 2.0 Authorization Code Credential Provider.

Many organizations require Credential Provider support for various 3rd party SaaS services which only support short lived credentials with the OAuth 2.0 Authorization Code Flow. These services included:

* Atlassian
* GitLab
* Slack
* GCP BigQuery
* Apigee
* PagerDuty

This beta release enables users to use 3rd party SaaS services and have short-lived access tokens generated on demand for authentication to APIs that these 3rd party services provide.

For more information on how to configure the OAuth 2.0 Authorization Code Credential Provider to be used with any of these 3rd party services, please see the [OAUth 2.0 Authorization Code Credential Provider](user-guide/access-policies/credential-providers/oauth-client-credentials.md) page.

## 2024-06-05

### Non-root Aembit containers and configurable Agent Proxy file descriptor limits

Aembit has released two new feature updates that enhance existing Aembit functionality.

#### Aembit Containers

All injected Aembit containers are now run as non-root users.

#### Agent Proxy File Descriptor Limits

Users may configure limits for the number of file descriptors Agent Proxy is allowed to open on a VM. You may configure this number when Agent Proxy is installed (using the `AEMBIT_FD_LIMIT` flag).

*virtual machines*

* *Default Limit* - 65535, set by Agent Proxy installer

* *Configuration* - This limit is configurable via the `AEMBIT_FD_LIMIT` environment variable. This value is passed directly to `systemd` in Agent Proxy’s service file at the time of installation.

* *Example* - `AEMBIT_FD_LIMIT=200000 [...] ./install`

*Kubernetes*

* *Default Limit* - This limit is inherited from container runtime.

* *Configuration* - There is no official support without modifying the underlying runtime. For more information on configuring these limits, please see the [Kubernetes limits support](https://github.com/kubernetes/kubernetes/issues/3595) GitHub thread.

*AWS ECS*

* *Default Limit* - 1024

* *Configuration* - This limit is configurable via the ECS Task Definition API or ECS Dashboard. Please refer to the [AWS ECS Developer Guide](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/fargate-tasks-services.html#fargate-resource-limits) for more detailed information on how to configure these limits.

*AWS Lambda*

* *Default Limit* - 1024

* *Configuration* - This limit is not configurable. For more information, please refer to the [AWS Lambda Developer Guide](https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html#function-configuration-deployment-and-execution).

## 2024-06-04

### AWS Role Trust Provider now available

Aembit has released an update to support AWS Role-Based Trust Providers.

The ability to create and use different types of Trust Providers in your Aembit environment enables you to have flexibility in how resources are managed. With this enhancement, you now have an additional option when selecting a Trust Provider.

For more information on AWS Role-Based Trust Providers, please see the [AWS Role Trust Provider](user-guide/access-policies/trust-providers/aws-role-trust-provider.md) page.

## 2024-05-30

### Resource Sets now available

Many organizations have certain security requirements that specify which resources should be managed by a group. To address these security needs, Aembit has released a new Resource Sets feature that enables you to determine which groups will have access to various resources.

You may find it necessary to segment management responsibilities for certain entities and resources in your Aembit environment between different individuals and groups for security reasons. To accommodate this requirement, Aembit has released the Resource Sets feature.

Resource Sets enable you to group entities and resources (e.g. Credential Providers, Trust Providers, Identity Providers, etc.) into a single collection and assign specific users to manage these resources.

For more detailed technical information on how to use create and manage Resource Sets, please refer to the [Resource Sets](user-guide/administration/resource-sets/overview.md) technical documentation.

## 2024-05-15

### Graceful Agent Proxy shutdown for sidecars

In some cases, you may find it necessary to manually shut down Agent Proxy when the main container exits, but a sidecar is still running. Since you may not want to kill the whole job, since it will look like a cancelled job, Aembit now provides a solution that enables you to gracefully terminate the job while allowing the sidecar to still run.

For more detailed information on this feature, please refer to the Agent Proxy Shutdown page.

## 2024-04-30

### AWS Lambda Container deployment now supported

There are many different deployment options you can currently use to deploy Aembit Edge Components in your environment, including GitHub Actions, GitLab Jobs, and Kubernetes.

To increase the available deployment options for our users, Aembit now provides support for users who wish to deploy Aembit Edge Components to an Amazon Web Services (AWS) Lambda Container.

For more detailed information on how to deploy Aembit Edge Components to AWS Lambda Containers, please refer to the [AWS Lambda Container](user-guide/deploy-install/serverless/aws-lambda-container.md) technical documentation.

## 2024-04-23

### GeoIP Access Conditions and Google Cloud Storage Log Streams now available

Aembit has released two new features on Aembit Cloud:

* Access Condition support for Geographic IP (GeoIP) restrictions
* Log Stream support for streaming to Google Cloud Storage Buckets

#### Aembit GeoIP Access Conditions

You may now configure and add Aembit GeoIP conditions in your Aembit Tenant. This new Access Condition type enables you to explicitly designate which countries/regions will have access to Server Workloads from policy-enabled Client Workloads.

For more information on this feature, please refer to the [Access Conditions for GeoIP Restriction](user-guide/access-policies/access-conditions/aembit-geoip.md) page.

#### Google Cloud Storage Bucket Log Streams

Aembit now supports Log Streams that target Google Cloud Storage (GCS) Buckets. You may add or configure this new Log Stream destination type in the Administration tab of your Aembit Tenant.

For more information on this feature, please refer to the [Google Cloud Storage Bucket Log Streams](user-guide/access-policies/access-conditions/aembit-geoip.md) page.

## 2024-04-08

### Red Hat 8.9 now supported for virtual machine deployments

Aembit Edge Components now support virtual machine deployments to virtual machines running Red Hat 8.9.

## 2024-04-04

### GitLab CI/CD Jobs now supported as Client Workloads

Aembit now supports GitLab CI/CD Jobs as Client Workloads.

For more information on how to configure GitLabs Jobs with Aembit Client Workloads, please refer to the [Script-based Agent](user-guide/deploy-install/ci-cd/gitlab/overview.md) page.

## 2024-03-19

### Automatic Kerberos attestation key rotation in Agent Controller

An issue was identified in the Agent Controller component due to the non-rotation of the public/private key pair used for Kerberos attestation. This issue has been resolved by implementing a process by which these private/public key pairs will be automatically rotated when the certificate reaches 80% of its lifespan.

## 2024-03-12

### Kerberos Trust Provider now available for Active Directory

Aembit has released a Kerberos Trust Provider that enables the attestation of Client Workloads running in virtual machine environments joined to Active Directory. This attestation method is specifically designed for on-premise deployments where alternative attestation methods, such as AWS or Azure metadata service trust providers, are not available.

For more detailed information on this Kerberos Trust Provider, please refer to the [Kerberos Trust Provider](user-guide/access-policies/trust-providers/kerberos-trust-provider.md) technical documentation.

## 2024-03-11

### TLS support between Agent Proxy and Agent Controller

Aembit now supports secure communication between Agent Proxy and Agent Controller using Transport Layer Security (TLS) for both Kubernetes and virtual machine deployments.

For more information on how to configure TLS for Agent Controller, please refer to the [Configuring TLS for Agent Controller](user-guide/deploy-install/advanced-options/agent-controller/configure-customer-pki-agent-controller-tls.md) documentation.

## 2024-03-09

### Aembit Terraform Provider now available

Aembit has officially released a Terraform Provider to the [Hashicorp Terraform Registry](https://registry.terraform.io/modules/Aembit/ecs/aembit/latest).

The Aembit Terraform Provider enables users to manage Aembit Cloud resources using terraform manually or via CI/CD workflows.

For more detailed information about the Aembit Terraform Provider, please see the [Aembit Terraform documentation](user-guide/access-policies/advanced-options/terraform/terraform-configuration.md).

## 2024-02-27

### SAML SSO authentication now available for administrators

Aembit now supports SAML/SSO authentication for administrators who wish to simplify the Aembit Tenant login process for their users. Instead of requiring a user to enter their username/password credentials every time a user tries to access the Aembit Tenant, users will now be able to use a 3rd party SAML SSO Provider (e.g. Google, Okta, Microsoft Entrata) to log into the tenant.

For more information on how to configure Identity Providers using SAML, please see the [Configuring Identity Providers](user-guide/administration/identity-providers/create-idp-saml.md) technical documentation.

## 2024-01-31

### Wiz integration now available for vulnerability assessment

Aembit now supports Wiz integration. Using the Wiz Integration API, you can work with both your Aembit Tenant and Wiz to identify customer assets and vulnerabilities.

For more detailed information about the Aembit -> Wiz integration, please refer to the [Wiz Integration page](user-guide/access-policies/access-conditions/integrations/wiz.md) on the Aembit technical documentation site.

## 2024-01-16

### Access Authorization Events and Google Cloud Run Jobs support now available

#### Support for Access Authorization Events

Aembit has now enabled support for Access Authorization Events. Access Authorization Events enable customers to observe credential requests.

#### Support for Google CloudRun Jobs as Client Workloads

Aembit supports Google CloudRun Jobs as Client Workloads. With this support, you can now:

* authenticate to the Aembit IdP using Attestation with the GCP Cloud Run Job Identity

* request and retrieve a secret from GCP Secret Manager

## 2024-01-15

### CrowdStrike integration now available for security posture checks

Aembit now supports integration with CrowdStrike. This integration allows you to leverage CrowdStrike’s service to prevent Server Workload access from Client Workloads that do not meet an expected state.

For more information about this integration, please refer to the [CrowdStrike Integration page](user-guide/access-policies/access-conditions/integrations/crowdstrike.md) on the Aembit technical documentation site.

## 2024-01-04

### Agent Controller high availability now supported

The Aembit Agent Controller can now be installed in high availability configurations. Because the Agent Controller is a critical Aembit Edge Component that manages Agent Proxy registration and credential acquisition for Aembit Cloud access, HA support was necessary to ensure the continuous availability of the Agent Controller.

For information on installing and configuring Agent Controller in high availability environments, please see the [Agent Controller High Availability](user-guide/deploy-install/advanced-options/agent-controller/agent-controller-high-availability.md) page.

## 2023-12-07

### CrowdStrike Falcon Sensor integration for virtual machine verification

In an effort to ensure **only** Client Workloads that run in a secure environment can access Server Workloads, Aembit has enabled integrations with CrowdStrike and its CrowdStrike Falcon Sensor. CrowdStrike Falcon Sensor checks multiple items on the virtual machine (VM) to verify the VM is secure.

## 2023-11-14

### MFA support and Linux virtual machine Edge deployment now available

Several new feature updates and additions have been made to improve Aembit user experience. These updates include:

* Admin console multi-factor authentication support
* Edge components VM deployment support

#### Multi-factor authentication support

Aembit now supports Multi-Factor Authentication (MFA) so users can provide different authentication methods. Users can:

* scan a QR code to configure their compatible authentication application
* retrieve MFA Recovery Codes in case the device or application is unavailable
* view the users who have configured MFA within the Aembit Users view.

#### Linux-based VM deployment support

Users may now deploy Aembit Edge Components to VMs (non-Kubernetes). This feature enables users to have options on how they want to deploy these components.

For more detailed information about this feature, please see the [virtual machine Installation](user-guide/deploy-install/virtual-machine/overview.md) page.

## 2023-10-16

### Dynamic Claims now available for Credential Providers

Aembit has released a new feature for Credential Providers called “Dynamic Claims.” This feature allows you to set the Subject claim and Custom claims with either literal strings or dynamic values when setting up Credential Providers in your Aembit client tenant.

For more detailed information about Dynamic Claims, please refer to [Dynamic Claims page](user-guide/access-policies/credential-providers/advanced-options/dynamic-claims-vault.md)

This feature is currently only supported for Vault integration.
