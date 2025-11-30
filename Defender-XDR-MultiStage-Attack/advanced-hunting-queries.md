## 🧪 LSASS MiniDump Attempt

```kql
DeviceProcessEvents
| where FileName =~ "rundll32.exe"
| where ProcessCommandLine contains "MiniDump"
```

![KQL Minidump](./Images/KQL-Minidump.PNG)
