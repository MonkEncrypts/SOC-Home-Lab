# MITRE ATT&CK Mapping

| Previous                                             | Current                  | Next                                        |
| ---------------------------------------------------- | ------------------------ | ------------------------------------------- |
| [← False Positives](../detection/false-positives.md) | **MITRE ATT&CK Mapping** | [Validation →](../validation/validation.md) |

---

> [!NOTE]
>
> **Document:** MITRE ATT&CK Mapping
>
> **Investigation Phase:** 8 of 10
>
> **Detection ID:** DET-002
>
> **MITRE ATT&CK Technique:** T1059.003 – Windows Command Shell
>
> **Status:** ✅ Completed

---

# MITRE ATT&CK Overview

This investigation maps to **MITRE ATT&CK Technique T1059.003 – Windows Command Shell**.

The ATT&CK framework documents techniques commonly used by adversaries during different stages of an intrusion. Mapping detections to ATT&CK techniques provides a standardized method for describing attacker behavior and helps defenders understand which adversary actions are visible within their environment.

---

# Technique Details

| Field | Value |
|------|-------|
| Framework | MITRE ATT&CK |
| Technique ID | T1059.003 |
| Technique Name | Windows Command Shell |
| Tactic | Execution |
| Platform | Windows |

---

# Why This Technique Applies

During this simulation, the attacker executed several native Windows command-line utilities through an interactive Command Prompt (`cmd.exe`) session.

The Command Prompt acted as the execution mechanism for each reconnaissance command, making Windows Command Shell the primary ATT&CK technique demonstrated during the investigation.

The following commands were executed:

```cmd
whoami
hostname
ipconfig
systeminfo
net user
tasklist
```

Although the individual utilities primarily perform reconnaissance, they were all executed through the Windows Command Shell, which aligns with ATT&CK Technique **T1059.003**.

---

# Investigation Evidence

The investigation confirmed this technique through multiple sources of evidence:

| Evidence | Validation |
|----------|------------|
| Command Prompt execution | ✅ Confirmed |
| Sysmon Event ID 1 generated | ✅ Confirmed |
| Process creation telemetry available | ✅ Confirmed |
| Splunk investigation completed | ✅ Confirmed |
| Parent-child process relationship observed | ✅ Confirmed |

Together, these artifacts demonstrate that the simulated command execution was successfully captured from endpoint execution through SIEM investigation.

---

# SOC Relevance

Windows Command Shell remains one of the most frequently abused execution mechanisms because it is present on every Windows system and enables attackers to execute a wide range of native commands without introducing additional binaries.

For SOC analysts, detecting the execution of `cmd.exe` alone is rarely sufficient. Greater investigative value comes from understanding:

- Which commands were executed.
- The parent process responsible for launching the shell.
- The execution sequence.
- The user context.
- The surrounding endpoint telemetry.

Behavioral analysis provides significantly higher detection fidelity than relying solely on the presence of the Command Prompt.

> [!TIP]
> MITRE ATT&CK mapping is not intended to label activity as malicious. Instead, it provides a common language that helps defenders classify observed behavior, compare detections, and identify gaps in defensive coverage.

---

# ATT&CK Coverage Summary

| Detection Capability | Status |
|----------------------|--------|
| Windows Command Shell execution identified | ✅ |
| Endpoint telemetry collected | ✅ |
| SIEM visibility confirmed | ✅ |
| Analyst investigation completed | ✅ |
| ATT&CK technique mapped | ✅ |

---

## Interview Insight

> **Interview Insight**
>
> During interviews, don't stop at saying *"This maps to T1059.003."*
>
> Explain **why** it maps to the technique. In this investigation, the attacker executed reconnaissance commands through the Windows Command Shell (`cmd.exe`), making the shell itself the execution mechanism represented by T1059.003. The reconnaissance commands are the actions performed through that execution mechanism.

---

## Next Step

➡ Continue to **[Validation](../validation/validation.md)**.