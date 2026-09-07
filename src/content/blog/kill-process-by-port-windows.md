---
title: "Kill a Process by Port in Windows"
pubDate: 2026-09-07
description: "Quickly kill a process occupying a specific port on Windows using PowerShell."
---

To kill a process running on a specific port in Windows, run this PowerShell command:

```powershell
Get-Process -Id (Get-NetTCPConnection -LocalPort yourPortNumber).OwningProcess | Stop-Process -Force
```

Replace `yourPortNumber` with the actual port (e.g., `3000`).
