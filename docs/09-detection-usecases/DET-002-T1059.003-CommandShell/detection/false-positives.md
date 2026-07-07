# False Positives

| Previous | Current | Next |
|----------|---------|------|
| [← Detection Logic](detection-logic.md) | **False Positives** | [MITRE Mapping →](../mitre/mitre-mapping.md) |

---

> [!NOTE]
>
> **Document:** False Positives
>
> **Investigation Phase:** 7 of 10
>
> **Detection ID:** DET-002
>
> **MITRE ATT&CK:** T1059.003 – Windows Command Shell
>
> **Status:** ✅ Completed

---

# Purpose

The objective of this document is to identify legitimate activities that may resemble the simulated reconnaissance behavior observed during this investigation.

Understanding potential false positives is essential when designing detections that balance visibility with operational practicality. High-quality detections should generate actionable alerts while minimizing unnecessary analyst workload.

---

# Potential False Positives

The following activities may generate telemetry similar to the simulated attack without representing malicious behavior.

| Legitimate Activity | Why It May Trigger Similar Telemetry |
|---------------------|--------------------------------------|
| System administration | Administrators frequently use native command-line utilities for troubleshooting and maintenance. |
| Helpdesk support | Support engineers commonly execute commands such as `whoami`, `hostname`, and `ipconfig` during user support sessions. |
| IT inventory collection | Asset management and inventory scripts may collect operating system, hardware, and network information. |
| Automation scripts | Administrative PowerShell or batch scripts may invoke multiple command-line utilities as part of routine tasks. |
| Software deployment and management tools | Enterprise management platforms may execute command-line utilities during software installation, configuration, or health checks. |

---

# Why These Are Not Automatically Malicious

Each command used during this investigation is a legitimate Windows utility included with the operating system.

Executing one or more of these commands does not, by itself, indicate malicious activity.

Instead, analysts should evaluate additional contextual information before determining whether an alert represents a genuine security incident.

Examples include:

- Parent process
- User account
- Logon session
- Execution frequency
- Command sequence
- Time of execution
- Associated endpoint activity

> [!IMPORTANT]
> Effective detections should prioritize behavioural context over individual executable names. A sequence of reconnaissance commands executed from an unusual process or account is significantly more suspicious than a single execution of `whoami.exe`.

---

# Strategies for Reducing False Positives

Potential approaches for improving detection fidelity include:

- Alert on multiple reconnaissance commands executed within a defined time window.
- Correlate command execution with interactive logon sessions.
- Exclude known administrative automation where appropriate.
- Prioritize investigations involving unusual parent processes.
- Combine endpoint telemetry with authentication and PowerShell logs for additional context.

These improvements help reduce unnecessary alerts while increasing confidence in the detection.

---

# Analyst Considerations

A SOC analyst should avoid classifying activity as malicious based solely on the execution of native Windows utilities.

Instead, analysts should evaluate the broader operational context and determine whether the observed behavior aligns with expected administrative activity or resembles adversary reconnaissance.

---

## Interview Insight

> **Interview Insight**
>
> A common interview question is:
>
> **"Would you create an alert for every execution of `whoami.exe`?"**
>
> The correct answer is **No**.
>
> Legitimate administrators, support engineers, and automation tools frequently execute this command. A stronger detection focuses on behavioural patterns, command sequences, execution context, and telemetry correlation rather than isolated process executions.

---

## Next Step

➡ Continue to **[MITRE Mapping](../mitre/mitre-mapping.md)**.