---
type: reference
title: "AgentControllerListDTO"
description: "Page of Agent Controllers for Agent Proxy management"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-09-22T20:22:25-07:00
---

# AgentControllerListDTO

Page of Agent Controllers for Agent Proxy management

**Type:** object

**Properties:**

- **page** *(optional)*: integer (int32) - Page of entities
- **perPage** *(optional)*: integer (int32) - Number of entities requested for the current page
- **order** *(optional)*: null,string - Ordering criteria used for the current page
- **statusCode** *(optional)*: integer (int32) - HTTP StatusCode for the current result
- **recordsTotal** *(optional)*: integer (int32) - Total number of AgentControllers available
- **agentControllers** *(optional)*: null,array - Page of AgentControllers for this request
