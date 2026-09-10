---
type: explanation
title: "About Colocating Aembit Edge Components"
description: "Considerations and best practices if colocating Aembit Edge Components"
resource: https://docs.aembit.io/user-guide/deploy-install/about-colocating-edge-components/
tags: ["deploy-install"]
timestamp: 2026-09-08T23:32:41-07:00
---

# About Colocating Aembit Edge Components

Deploying Aembit’s Edge Components is all about balancing security, scalability, and operational simplicity. Ideally, the Agent Controller and Agent Proxy should run on separate machines. However, in some situations—perhaps for a test environment or because of infrastructure limitations—you may have no choice but to colocate them. If that’s the case, understanding the risks and following best practices can help you minimize issues.

## Why Aembit recommends separating Edge Components

Keeping Agent Controller and Agent Proxy on separate machines is the best way to make sure they remain resilient and secure.

Colocating Edge Components introduces a single point of failure, which can disrupt both traffic interception (Proxy) and trust anchor services (Controller) at the same time.

Security is another major concern. Agent Controller and Agent Proxy serve distinct roles, and combining them on one machine increases the potential impact of a compromise. If an attacker breaches the host, they gain access to both components, expanding their reach.

Colocation also limits your ability to scale efficiently. The Agent Proxy may require more CPU or memory during high traffic periods, and colocating it with the Agent Controller makes it harder to allocate additional resources where needed.

Lastly, colocation can complicate your network design. The Agent Proxy must sit in a position to intercept workload traffic, while the Agent Controller belongs in a more secure, isolated network segment. Finding a placement that serves both roles effectively can be challenging.

## When colocation might be the right choice

While Aembit recommends separate deployments, there may be times when colocation is your only option. In smaller test environments, proof-of-concept setups, or resource-constrained scenarios, colocating the Agent Controller and Proxy may be acceptable. When this happens, taking steps to mitigate risk is essential.

## Best Practices for colocating Edge Components

If you must colocate, follow these guidelines to reduce risk and maintain performance:

* **Harden the host machine** - Apply stricter security controls, such as enhanced monitoring, restricted access, and regular audits.

* **Allocate sufficient resources** - Ensure the host has enough CPU, memory, and network bandwidth to support both components without performance degradation.

* **Plan for recovery** - Develop clear recovery procedures to minimize downtime if the colocated host fails.

* **Carefully design your network** - Ensure the Agent Proxy can intercept workload traffic while maintaining secure access to the Agent Controller’s trust anchor services.

## Making the best decision for your environment

Colocating Aembit’s Edge Components should be a last resort, not a first choice. When separation isn’t possible, understanding the risks and applying best practices can help you maintain a secure and stable deployment. By taking these steps, you can make sure your environment remains resilient, even in less-than-ideal circumstances.
