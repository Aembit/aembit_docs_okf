---
type: reference
title: "ProcessDTO"
description: "Process information for Client Workload identification"
resource: https://docs.aembit.io/api-guide/edge/api-reference-edge/
interface: api
timestamp: 2026-05-07T11:06:15-07:00
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
