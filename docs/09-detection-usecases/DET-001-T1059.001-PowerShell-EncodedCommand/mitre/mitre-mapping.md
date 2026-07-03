# MITRE ATT&CK Mapping

## Detection Overview

| Property | Value |
|----------|-------|
| Detection ID | DET-001 |
| Detection Name | Suspicious PowerShell Encoded Command |
| Status | Validated |
| Platform | Windows |
| SIEM | Splunk Enterprise |
| Telemetry Source | Sysmon Event ID 1 |

---

## MITRE ATT&CK Mapping

| ATT&CK Field | Value |
|--------------|-------|
| Tactic | Execution |
| Technique | T1059 |
| Sub-technique | T1059.001 |
| Technique Name | PowerShell |

---

## Detection Rationale

Attackers frequently execute PowerShell using the `-EncodedCommand` parameter to obfuscate malicious scripts and evade basic command-line inspection.

Sysmon Event ID 1 captures the complete process creation event, including the command line used to launch PowerShell. This enables analysts to identify encoded PowerShell execution through process creation telemetry.

---

## Detection Objective

Detect PowerShell executions containing the `-EncodedCommand` parameter and provide analysts with sufficient telemetry to investigate:

- Executing user
- Parent process
- Full command line
- Process lineage
- Integrity level
- Process hashes

---

## Data Source

- Sysmon Event ID 1 (Process Create)

---

## ATT&CK Data Sources

- Process Creation
- Command Execution
- Windows Event Logs