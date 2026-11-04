---
type: reference
title: "AgentControllerPatchDTO"
description: "Patch Request DTO for individual Agent Controller"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-11-03T18:06:18-08:00
---

# AgentControllerPatchDTO

Patch Request DTO for individual Agent Controller

**Type:** object

**Properties:**

- **version** *(optional)*: null,string
- **isActive** *(optional)*: null,boolean (boolean) - New Status for the identified Agent Controller
- **trustProviderId** *(optional)*: null,string (uuid) - New Trust Provider to use for the identified Agent Controller
- **isDebugMode** *(optional)*: null,boolean (boolean) - Enable or disable debug mode for the identified Agent Controller
- **debugDurationSeconds** *(optional)*: null,integer (int32) - Debug mode duration in seconds (optional, defaults to 3600)
- **debugModeExpiresAt** *(optional)*: null,string (date-time) - Debug Mode expiration timestamp of the Agent Controller
