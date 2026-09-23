---
type: reference
title: "ProcessDTO"
description: "Process information for Client Workload identification"
resource: https://docs.aembit.io/dev-guide/api/edge/api-reference-edge/
interface: api
timestamp: 2026-09-22T20:31:55-07:00
---

# ProcessDTO

Process information for Client Workload identification

**Type:** object

**Properties:**

- **name** *(optional)*: null,string - Process name
- **pid** *(optional)*: integer (int32) - Process identifier (PID)
- **userId** *(optional)*: integer (int32) - User identifier running the process
- **userName** *(optional)*: null,string - Username running the process
- **exePath** *(optional)*: null,string - Executable file path of the process
- **commandLine** *(optional)*: null,string - Command line running the process
- **exeHash** *(optional)*: null,string - Executable hash of the process
