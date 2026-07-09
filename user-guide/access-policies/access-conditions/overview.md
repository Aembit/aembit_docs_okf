---
type: explanation
title: "Access Conditions"
description: "This document provides a high-level description of Access Conditions"
resource: https://docs.aembit.io/user-guide/access-policies/access-conditions/
tags: [access-condition, access-policy]
timestamp: 2026-01-30T00:27:24-08:00
type_inferred: true
---

# Access Conditions

Access Conditions are rules and conditions that evaluate an Access Policy and determine whether a Client Workload should receive access to a Server Workload. Whenever the system receives a request for access to an Access Policy and/or Credential, these Access Conditions validate and verify the request.

If validation passes, the system grants the request; however, if validation fails, the system denies the request. For an Access Condition to validate and verify a request, administrators must already establish an existing integration and create an Access Policy.

Reuse this in another Resource Set

You can copy an Access Condition to another Resource Set. See [About component copying](../../administration/resource-sets/about-component-copying.md) to learn more.

## Available Access Conditions

[Section titled “Available Access Conditions”](#available-access-conditions)

* [Geo-IP-based Conditions](aembit-geoip.md) - Control access based on geographic location using IP address geolocation.
* [Time-based Conditions](aembit-time-condition.md) - Enforce access restrictions based on time of day, day of week, or specific date ranges.
* [CrowdStrike Conditions](crowdstrike.md) - Integrate with CrowdStrike to evaluate the security posture of Client Workloads and enforce access based on threat intelligence.
* [Wiz Conditions](wiz.md) - Leverage Wiz security posture assessments to ensure Client Workloads meet compliance and security requirements.

## Available security tool integrations

[Section titled “Available security tool integrations”](#available-security-tool-integrations)

* [CrowdStrike](integrations/crowdstrike.md) - Integrates with CrowdStrike to evaluate endpoint security posture.
* [Wiz](integrations/wiz.md) - Integrates with Wiz to assess cloud security posture.
