---
type: reference
title: "AgentControllerListDTO"
description: "Page of Agent Controllers for Agent Proxy management"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
timestamp: 2025-08-28T08:38:37-04:00
---

# AgentControllerListDTO

Page of Agent Controllers for Agent Proxy management

**Type:** object

**Properties:**

- **page** *(optional)*: integer (int32) - Page of entities
- **perPage** *(optional)*: integer (int32) - Number of entities requested for the current page
- **order** *(optional)*: string | null - Ordering criteria used for the current page
- **statusCode** *(optional)*: integer (int32) - HTTP StatusCode for the current result
- **recordsTotal** *(optional)*: integer (int32) - Total number of AgentControllers available
- **agentControllers** *(optional)*: Array of [AgentControllerDTO](agent-controller-dto.md) - Page of AgentControllers for this request
