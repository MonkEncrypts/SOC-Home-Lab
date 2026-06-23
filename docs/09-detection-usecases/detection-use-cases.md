# Detection Use Cases — MITRE ATT&CK Mapped Detections

## Status: Planned — Pre-Attack Phase

This lab's telemetry pipeline (Sysmon → Universal Forwarder → Splunk) is fully operational and verified (see [Log Verification](../08-log-verification/splunk-search-validation.md)). Detection content — Splunk searches written against this telemetry and mapped to MITRE ATT&CK — is the current and next phase of work.

This document is structured as the working framework for that detection engineering effort, with the current state of each use case tracked explicitly.

---

## Detection Framework

Each detection use case in this lab will be documented using the following structure, modeled on how detection content is typically documented in production SOC environments:

```
## [Detection Name]

**MITRE ATT&CK Technique:** [Technique ID and Name]
**Tactic:** [Tactic Name]
**Data Source:** [Sysmon Event ID(s)]

### Detection Logic
[Splunk SPL search]

### Rationale
[Why this behavior is suspicious / what legitimate activity might also trigger it]

### Validation
[How the detection was tested — e.g., simulated via Kali Linux attack, Atomic Red Team, or manual reproduction]

### False Positive Considerations
[Known legitimate activity that could trigger this detection]
```

---

## Planned Detection Use Cases

| # | Detection | ATT&CK Technique | Tactic | Sysmon Event ID | Status |
|---|---|---|---|---|---|
| 1 | Suspicious PowerShell Execution (encoded commands) | T1059.001 | Execution | Event ID 1 | 📋 Planned |
| 2 | LSASS Memory Access (credential dumping) | T1003.001 | Credential Access | Event ID 10 | 📋 Planned |
| 3 | Outbound Beaconing to Uncommon Ports | T1071.001 | Command and Control | Event ID 3 | 📋 Planned |
| 4 | Registry Run Key Persistence | T1547.001 | Persistence | Event ID 12 / 13 | 📋 Planned |
| 5 | Suspicious Service Creation | T1543.003 | Persistence | Event ID 1 | 📋 Planned |
| 6 | Living-off-the-Land Binary Abuse (LOLBins) | T1218 | Defense Evasion | Event ID 1 | 📋 Planned |
| 7 | Unusual Parent/Child Process Relationships | T1055 | Defense Evasion / Privilege Escalation | Event ID 1 | 📋 Planned |

---

## Example Detection (Template — Not Yet Validated)

The following illustrates the intended format for completed detections, using a common, well-documented technique as an example. **This detection has not yet been tested against a live attack simulation in this lab** and is included to demonstrate the analytical approach.

### Suspicious PowerShell Execution (Encoded Command)

**MITRE ATT&CK Technique:** T1059.001 — Command and Scripting Interpreter: PowerShell
**Tactic:** Execution
**Data Source:** Sysmon Event ID 1 (Process Creation)

**Detection Logic (draft SPL):**
```spl
index=main source="WinEventLog:Microsoft-Windows-Sysmon/Operational" EventID=1
Image="*\\powershell.exe"
CommandLine="*-EncodedCommand*" OR CommandLine="*-enc*"
| table _time, Computer, User, Image, CommandLine, ParentImage
```

**Rationale:** Base64-encoded PowerShell commands are a common technique for obfuscating malicious scripts from simple string-matching defenses and for evading command-line logging readability. Legitimate administrative use of `-EncodedCommand` exists but is comparatively rare in normal end-user activity.

**False Positive Considerations:** Some legitimate enterprise software deployment and configuration management tools (e.g., certain SCCM or Group Policy script wrappers) use encoded PowerShell as part of normal operation. Any production deployment of this detection would need an allowlist of known-legitimate parent processes or signed script sources.

---

## Next Steps for This Section

1. Configure Kali Linux as an active attack simulation platform (network connectivity already established — see [Network Architecture](../02-architecture/network-architecture.md)).
2. Execute controlled, documented attack techniques against the Windows 10 endpoint (e.g., using Atomic Red Team for safe, repeatable technique simulation).
3. Capture the resulting Sysmon telemetry in Splunk for each technique.
4. Write and validate a Splunk detection search for each technique.
5. Document each completed detection using the framework above, replacing the "Planned" status with "Validated" and linking the supporting screenshot evidence.
