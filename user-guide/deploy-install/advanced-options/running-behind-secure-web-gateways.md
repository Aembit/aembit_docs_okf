---
type: how-to
title: "Running Edge Components behind TLS-inspecting proxies"
description: "How to run Agent Controller and Agent Proxy behind a TLS-inspecting Secure Web Gateway such as Zscaler"
resource: https://docs.aembit.io/user-guide/deploy-install/advanced-options/running-behind-secure-web-gateways/
interface: web-ui
tags: ["advanced-option", "deploy-install"]
timestamp: 2026-09-16T07:24:16-07:00
---

# Running Edge Components behind TLS-inspecting proxies

A Secure Web Gateway (SWG) such as Zscaler or Netskope sits in the egress path. It terminates Transport Layer Security (TLS) with a certificate from a Certificate Authority (CA) that the clients on the network trust, then re-encrypts the traffic to its destination. Products in this category also offer secure tunnels, a form of Virtual Private Network (VPN) with per-destination tunneling policies.

Agent Controller and Agent Proxy hold long-lived gRPC connections over HTTP/2 to Aembit Cloud, and many TLS-inspecting devices downgrade or interrupt that traffic pattern. This page covers two ways to run Aembit Edge on such a network. Exempting the Aembit control plane from inspection is the recommended posture. Where your security team declines an exemption, you can instead configure the components to tolerate inspection.

## How TLS inspection affects Edge Components

Three distinct failures account for most Edge Component problems on an inspected network.

| Component        | Failure                                                 | What the customer sees                                                                                                                              |
| ---------------- | ------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| Agent Proxy      | The interception CA isn’t trusted                       | `invalid peer certificate: UnknownIssuer`, wrapped in `failed to connect to {url}: ...` or `TLS handshake through upstream HTTP proxy failed: ...`  |
| Agent Proxy      | HTTP/2 isn’t negotiated                                 | `HTTP/2 was not negotiated.` on a direct connection                                                                                                 |
| Agent Proxy      | The gateway drops an idle connection without closing it | `No GetCommands prompt received from server. Assuming channel is broken.`                                                                           |
| Agent Controller | The interception CA isn’t trusted                       | A TLS handshake failure on the outbound connection to Aembit Cloud                                                                                  |
| Agent Controller | The gRPC stream is reset or refused                     | `The gRPC stream was reset by the server.` or `The gRPC service is unavailable.`, both raised through `Error while getting Get Commands from gRPC.` |

Neither component falls back to HTTP/1.1. A gateway that downgrades or refuses HTTP/2 breaks the control-plane connection outright.

An idle timeout shorter than 60 seconds is the most disruptive gateway setting. Agent Proxy exchanges messages over the connection every 60 seconds, so a gateway that drops the connection sooner forces a reconnect on every cycle, and the connection never settles.

## Recommended: exempt control-plane traffic from inspection

Configure the gateway with a rule that exempts Aembit control-plane destinations from TLS inspection. In Zscaler this is an SSL Inspection policy rule with a **Do Not Inspect** action, plus a client-side bypass if Zscaler Client Connector is in the path. Netskope has an equivalent SSL decryption bypass.

### Destinations to exempt

Each of the following destinations is HTTPS on port 443. Replace `<tenantId>` with your Aembit Tenant ID. Replace `useast2.aembit.io` with the domain of your Stack if your Tenant runs on a different one.

| Destination                        | Purpose                                                     |
| ---------------------------------- | ----------------------------------------------------------- |
| `<tenantId>.ec.useast2.aembit.io`  | gRPC control plane, including the long-lived command stream |
| `<tenantId>.id.useast2.aembit.io`  | OAuth and OpenID Connect (OIDC) token endpoint              |
| `<tenantId>.api.useast2.aembit.io` | Tenant API, including cloud registration                    |
| `<tenantId>.aembit.io`             | Aembit Tenant UI                                            |
| `releases.aembit.io`               | Edge Component installer downloads                          |

### Gateway policy requirements

The exemption alone isn’t sufficient if the surrounding policy still constrains the connection. Whether the Aembit destinations fall under a dedicated policy or the default one, the policy that applies to them must:

* Exempt them from TLS inspection.
* Pass HTTP/2 through without downgrading it to HTTP/1.1.
* Allow a connection to stay open for as long as the component holds it, because the command stream is long-lived by design.
* Keep idle connections open for longer than 60 seconds, the interval at which Agent Proxy exchanges messages on the connection.

If possible, give the Aembit destinations a dedicated policy, so that these settings don’t loosen the default policy for every other destination.

### The case for an exemption

Security teams grant exemptions against a rationale rather than a request. The following points describe the traffic, and you can hand them to a reviewer as they stand.

* The traffic is control plane only: policy retrieval, workload attestation, credential brokering, and health reporting. It carries no user web browsing and isn’t a general-purpose data path.
* It’s gRPC over HTTP/2 with long-lived bidirectional streams, a pattern TLS-inspecting proxies mishandle. Many downgrade it to HTTP/1.1 or drop idle streams without signaling the drop.
* Aembit authenticates each connection at the application layer with short-lived tokens issued through attestation. Inspecting the payload yields little additional security while breaking the transport.
* The destinations are a small, fixed, Tenant-specific set of Aembit domains, so the exemption stays narrow and auditable.
* SWG vendors maintain their own exemption lists for applications that interception breaks, including certificate-pinned applications and gRPC services. An Aembit exemption follows that established pattern.

## Fallback: run Edge Components through inspection

Where your security team declines an exemption, configure both components to work through the inspecting gateway. The [Edge Component environment variables reference](../../../reference/edge-components/edge-component-env-vars.md) documents every variable named here in full.

### Trust the interception CA

Agent Proxy and Agent Controller use different mechanisms, and only Agent Proxy has a component-level setting.

**Agent Proxy.** Set [`AGENT_TRUST_PATH`](../../../reference/edge-components/edge-component-env-vars.md#agent_trust_path) to the path of a PEM-encoded CA certificate. This replaces the system trust store for the connection to Aembit Cloud rather than adding to it.

> **AGENT\_TRUST\_PATH reads only the first certificate**
>
> Agent Proxy reads only the first certificate in the file that `AGENT_TRUST_PATH` points at. Point the variable at a file holding the gateway’s root CA certificate alone.

**Agent Controller.** Agent Controller has no component-level trust setting. Install the interception CA in the operating system trust store, which Agent Controller uses to validate outbound connections. The `TLS_PEM_PATH` and `TLS_KEY_PATH` variables configure the Agent Controller’s own inbound server certificate and have no effect on outbound trust.

For private CAs on Server Workload connections rather than control-plane connections, see [Trusting certificates issued by private CAs](trusting-private-cas.md).

### Route outbound traffic through the gateway

Both components read `HTTP_PROXY`, `HTTPS_PROXY`, and `NO_PROXY`, in upper and lower case. Agent Controller has honored them since 1.30.3384 and Agent Proxy since 1.31.4670.

Give the proxy URL the `http` scheme and an explicit port, as in `http://proxy.example.com:8080`. Without an explicit port, it uses port 80.

Neither installer accepts these variables, so set them in the service environment:

* **Linux:** add them to the service’s systemd unit or a drop-in file with the `Environment=` directive. The unit doesn’t inherit a login shell’s environment.
* **Windows:** set them as machine-level environment variables, then restart the Windows service, `AembitAgentProxy` or `AembitAgentController`.

Startup diagnostics don’t log the proxy variables, so confirm the values from the service environment rather than from the component’s log.

Include the cloud metadata endpoints your hosts use in `NO_PROXY`, because workload attestation must reach them directly:

* `169.254.169.254` for the Amazon Web Services (AWS) and Azure instance metadata services
* `169.254.170.2` for the AWS Elastic Container Service (ECS) task metadata endpoint
* `metadata.google.internal` for the Google Cloud metadata server

Add the Agent Controller’s own address to `NO_PROXY` on Agent Proxy hosts, so that Agent Proxy reaches Agent Controller directly rather than through the gateway.

### Keep long-lived connections alive

An inspecting gateway that reaps idle connections without closing them leaves a component holding a connection that no longer carries traffic. Keep-alive pings let the component detect the dead connection and reconnect.

**Agent Proxy.** Set [`AEMBIT_TENANT_GRPC_PING_INTERVAL_SECS`](../../../reference/edge-components/edge-component-env-vars.md#aembit_tenant_grpc_ping_interval_secs) and [`AEMBIT_TENANT_GRPC_PING_TIMEOUT_SECS`](../../../reference/edge-components/edge-component-env-vars.md#aembit_tenant_grpc_ping_timeout_secs), which are off by default. Set the interval shorter than the gateway’s idle timeout. Both variables are available on Linux from Agent Proxy 1.32.4999 and through the Windows installer from 1.34.5755.

**Agent Controller.** Keep-alive pings are always on and aren’t configurable. Agent Controller sends a ping every 30 seconds and treats the connection as dead if no reply arrives within five seconds. It sends these pings even when the connection carries no traffic.

### What still degrades

Some inspection devices break gRPC bidirectional streaming even with the CA trusted and keep-alives enabled. The consequence depends on whether the gateway blocks the command stream alone or the whole channel.

**The gateway blocks the command stream.** The command stream carries only a health-check trigger from Aembit Cloud. Tenant health and status reporting degrades, and everything else continues, because the remaining operations are single request-response gRPC calls on the same channel. Agent Controller falls back to reporting health every 70 seconds on its own initiative, which restores status reporting without the stream.

**The gateway blocks the whole channel.** Credential brokering, token retrieval, and certificate operations all fail, because they share the channel with the command stream. This is an outage rather than a degradation, and it needs the exemption described earlier on this page.

## Server Workload traffic

The preceding guidance covers control-plane connections between Edge Components and Aembit Cloud. Traffic that Agent Proxy forwards on behalf of a Client Workload to a Server Workload is a separate concern.

* Keep the gateway policies that applied to those Server Workload destinations before you introduced Aembit.
* Configure Agent Proxy to time out connections before the upstream gateway or tunnel policy times them out.
* Expect added latency and lower throughput, in an amount that depends on the traffic forwarding options configured in the gateway.
* Agent Proxy routes only HTTP traffic through an upstream proxy. MySQL, PostgreSQL, Redis, Oracle, Snowflake, and TCP passthrough connections open a direct connection regardless of the proxy variables.

## Related pages

* [Edge Component environment variables reference](../../../reference/edge-components/edge-component-env-vars.md)
* [Trusting certificates issued by private CAs](trusting-private-cas.md)
* [Changing Agent log levels](changing-agent-log-levels.md)
