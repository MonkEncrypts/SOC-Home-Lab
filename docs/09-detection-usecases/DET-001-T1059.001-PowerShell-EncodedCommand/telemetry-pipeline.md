
# Telemetry Pipeline

## Detection Data Flow

```text
PowerShell Execution
        │
        ▼
Windows Process Creation
        │
        ▼
Sysmon Event ID 1
(Process Create)
        │
        ▼
Microsoft-Windows-Sysmon/Operational
        │
        ▼
Splunk Universal Forwarder
        │
        ▼
TCP 9997
        │
        ▼
Splunk Enterprise
        │
        ▼
SPL Detection
        │
        ▼
SOC Analyst Investigation
```

## Description

This detection relies on Sysmon Event ID 1 (Process Create) telemetry. The Splunk Universal Forwarder collects Sysmon Operational logs from the Windows endpoint and forwards them to Splunk Enterprise over TCP port 9997. Splunk indexes the events, enabling analysts to identify suspicious PowerShell executions containing the `-EncodedCommand` parameter.