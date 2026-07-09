---
type: explanation
title: "Discovery overview"
description: "What Aembit Discovery is and how it works"
resource: https://docs.aembit.io/user-guide/discovery/
tags: [discovery]
timestamp: 2025-05-29T11:26:12-07:00
type_inferred: true
---

# Discovery overview

Note

This is a beta feature and may be subject to changes.

To increase visibility and automatically identify workloads across your infrastructure, Aembit offers Discovery— a feature that helps you build a central, scalable view of your workloads.

Discovery improves your workload identity and access management (IAM) strategy by uncovering:

* Workloads you want to manage through Aembit but haven’t yet,
* Workloads you didn’t know Aembit could manage, or
* Workloads you didn’t even know existed.

Discovery serves three key purposes:

* **Visibility** - Rapidly surface workloads across edge and cloud environments, enabling you to track and manage resources throughout your infrastructure.
* **Scalability** - Create a centralized inventory of workloads, making it easier to manage and maintain visibility as your environment grows.
* **Access control** - Define Access Policies for discovered workloads to enforce security rules and simplify workload-to-workload access management.

## How discovery works

[Section titled “How discovery works”](#how-discovery-works)

Discovery uses [Discovery Sources](sources/overview.md) to find workloads in your environment. A Discovery Source is any mechanism Aembit uses to collect data about workloads for categorization and management.

* Aembit’s built-in Discovery Source—[Aembit Edge](sources/aembit-edge.md)—discovers workloads within the same environment where Edge Components (for example Agent Proxy) are deployed.
* Discovery can also integrate with third-party platforms like [Wiz](sources/wiz.md) to expand workload visibility across your cloud infrastructure.

Once Aembit collects this data, it categorizes workloads as either:

* **Managed** - Workloads that Aembit has explicitly reviewed and configured. Managed workloads are a core part of Aembit’s IAM system—they’re eligible for Access Policy evaluation and enforcement.

* **Discovered** - Workloads automatically found by Aembit from different sources. Discovered workloads are workloads that you’ve yet to review or convert to **Managed**—they don’t participate in Access Policy evaluation until that happens.

## Additional resources

[Section titled “Additional resources”](#additional-resources)

* [Discovery Sources overview](sources/overview.md)
* [Discovery Sources - Aembit Edge](sources/aembit-edge.md)
* [Discovery Sources - Wiz](sources/wiz.md)
