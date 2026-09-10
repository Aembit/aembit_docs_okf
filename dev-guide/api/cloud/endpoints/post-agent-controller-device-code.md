---
type: reference
title: "POST /api/v1/agent-controllers/{agentControllerExternalId}/device-code"
description: "Generate a Device Code for an Agent Controller"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["agent-controller"]
timestamp: 2026-07-28T14:42:14-04:00
---

# POST /api/v1/agent-controllers/{agentControllerExternalId}/device-code

Generate a Device Code for an Agent Controller.

**Operation ID:** `post-agent-controller-device-code`

**Parameters:**

- **agentControllerExternalId** (path) *(required)*: string (uuid) - ID of Agent Controller

**Responses:**

- **201**: Agent Controller Device Code
  - `application/json`: [AgentControllerDeviceCodeDTO](../schemas/agent-controller-device-code-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X POST \
  -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/agent-controllers/{agentControllerExternalId}/device-code"
```
