
# DET-001 T1059.001 - Suspicious PowerShell Encoded Command

## Detection Metadata

| Property               | Value                                 |
| ---------------------- | ------------------------------------- |
| Detection ID           | DET-001                               |
| Detection Name         | Suspicious PowerShell Encoded Command |
| Status                 | ✅ Validated                           |
| MITRE ATT&CK Technique | T1059.001                             |
| ATT&CK Tactic          | Execution                             |
| Severity               | Medium                                |
| Data Source            | Sysmon Event ID 1                     |
| SIEM                   | Splunk Enterprise                     |
| Detection Type         | Process Creation                      |
| Telemetry Source       | Microsoft-Windows-Sysmon/Operational  |

---
## Overview

This detection focuses on identifying suspicious PowerShell execution using the `-EncodedCommand` parameter. Adversaries frequently encode PowerShell commands in Base64 to conceal malicious activity from defenders. The objective of this detection is to validate that Sysmon captures the complete process creation event and that Splunk can reliably identify encoded PowerShell executions using command-line analysis.

---

## Threat Background

PowerShell is a legitimate Windows administration and automation tool that is widely used by system administrators. Because it is installed by default on Windows systems, attackers frequently abuse it as a Living-off-the-Land (LotL) binary to execute malicious commands without introducing additional executables.

One common technique is the use of the `-EncodedCommand` parameter, which allows attackers to provide a Base64-encoded PowerShell script instead of plain-text commands. Although Base64 encoding is not encryption, it obscures the command from casual inspection and is commonly observed in malware, penetration testing frameworks, and post-exploitation toolkits.

This detection aims to identify PowerShell processes executed with encoded command-line arguments using Sysmon process creation telemetry.

---

## Detection Objective

Detect PowerShell processes executed with Base64-encoded command-line arguments using Sysmon Event ID 1 telemetry collected by Splunk.

---

## Detection Hypothesis

If a PowerShell process is executed using the `-EncodedCommand`, `-enc`, or `-e` parameter, Sysmon Event ID 1 will capture the process creation event, including the full command line. Splunk can then identify suspicious encoded PowerShell execution through analysis of the `CommandLine` field.

---

## Detection Scope

| Included | Excluded |
|----------|----------|
| PowerShell Process Creation | PowerShell Script Block Logging |
| Sysmon Event ID 1 | AMSI Events |
| Command Line Analysis | PowerShell Operational Logs |
| Process Metadata | Network Connections |

---

## Lab Environment

| Component | Description |
|----------|-------------|
| SIEM | Splunk Enterprise |
| Endpoint | Windows 10 Virtual Machine |
| Attacker | Kali Linux Virtual Machine |
| Telemetry | Sysmon |
| Log Forwarding | Splunk Universal Forwarder |
| Network | VMware Host-Only Network |

---

## Expected Telemetry

| Field | Expected Value |
|------|----------------|
| Event ID | 1 |
| Image | powershell.exe |
| CommandLine | Contains `-EncodedCommand`, `-enc`, or `-e` |
| ParentImage | cmd.exe or explorer.exe |
| User | Logged-in Windows User |
| ProcessGuid | Present |
| ParentProcessGuid | Present |
| Hashes | Present |

---

## Attack Plan

The following activities will be performed during validation:

1. Generate a harmless Base64-encoded PowerShell command.
2. Execute the encoded command on the Windows endpoint.
3. Verify that Sysmon logs the process creation event.
4. Confirm successful ingestion into Splunk.
5. Analyze the captured event fields.
6. Develop an SPL detection query.
7. Validate the detection against collected telemetry.
8. Document the complete investigation.

---

## Investigation Questions

The investigation will answer the following questions:

- Who executed the PowerShell process?
- Which user account was involved?
- Which parent process launched PowerShell?
- What was the complete command line?
- Can the Base64 payload be decoded?
- What action did the decoded script perform?
- Which Sysmon fields are most useful?
- Which MITRE ATT&CK technique does this activity map to?
- Were any child processes spawned?
- What legitimate scenarios could generate similar events?

---

## Expected Outcome

Successful completion of this detection should demonstrate that:

- Sysmon captures the PowerShell process creation event.
- Splunk successfully ingests the telemetry.
- The encoded command is visible in the `CommandLine` field.
- A reliable SPL detection identifies encoded PowerShell execution.
- The detection is mapped to the appropriate MITRE ATT&CK technique.
- The investigation process and evidence are fully documented.

---

## Documentation Index

### Core Documentation

- [Detection Evolution](./detection-evolution.md)
- [Telemetry Analysis](./telemetry-analysis.md)
- [Telemetry Pipeline](./telemetry-pipeline.md)
- [MITRE ATT&CK Mapping](./mitre/mitre-mapping.md)
- [Analyst Playbook](./analyst-playbook.md)
- [Lessons Learned](./lessons-learned.md)
- [Validation](./validation.md)
  
### Evidence

- [Event Field Analysis](./evidence/event-fields.md)
  
### Investigation

- [Investigation Notes](./investigation/investigation-notes.md)
