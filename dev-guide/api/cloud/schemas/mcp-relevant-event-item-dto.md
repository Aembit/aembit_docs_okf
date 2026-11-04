---
type: reference
title: "McpRelevantEventItemDTO"
description: "A relevant event item in the chronological lifecycle of an MCP request"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-11-03T18:06:18-08:00
---

# McpRelevantEventItemDTO

A relevant event item in the chronological lifecycle of an MCP request

**Type:** object

**Properties:**

- **eventId** *(optional)*: string (uuid) - Unique identifier of the event
- **time** *(optional)*: null,string (date-time) - Timestamp when the event occurred
- **source** *(optional)*: null,string - Event source boundary (e.g. Workload, Access)
- **eventType** *(optional)*: null,string - Specific event type (e.g. mcp.request, access.authorization)
- **boundary** *(optional)*: null,string - Directional boundary label (e.g. MCP client → Gateway, Gateway → MCP server)
- **summary** *(optional)*: null,string - Human-readable event summary
- **status** *(optional)*: null,string - Event outcome or authorization status
- **detailsAvailable** *(optional)*: boolean - Indicates whether raw event payload is available
- **externalId** *(optional)*: null,string (uuid) - External correlation identifier if applicable
- **rawPayload** *(optional)*: null - Raw event payload if available
