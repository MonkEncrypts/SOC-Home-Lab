# Attack Scenario

| Previous                 | Current             | Next                                      |
| ------------------------ | ------------------- | ----------------------------------------- |
| [← README](../README.md) | **Attack Scenario** | [Attack Execution →](attack-execution.md) |

---

# Attack Scenario

## Objective

The objective of this simulation was to emulate an attacker performing post-compromise host reconnaissance using the native Windows Command Shell (`cmd.exe`). Rather than executing malware or exploiting vulnerabilities, the attacker leveraged legitimate Windows command-line utilities to collect information about the compromised system.

This activity represents one of the earliest phases following initial access, where an adversary attempts to understand the target environment before deciding on subsequent actions such as privilege escalation, lateral movement, persistence, or credential access.

---

## Scenario Description

Assume an attacker has successfully obtained an interactive command-line session on a Windows endpoint.

Before taking further action, the attacker begins gathering basic information about the compromised host to answer questions such as:

- Which user account is currently logged in?
- What is the hostname of the system?
- What network configuration does the host have?
- Which operating system and hardware are present?
- Which local user accounts exist?
- Which processes are currently running?

To answer these questions, the attacker executes several native Windows commands through the Command Prompt (`cmd.exe`).

Although each command is legitimate and commonly used by system administrators, the sequence of commands collectively resembles behavior frequently observed during the reconnaissance phase of real-world intrusions.

---

## Attacker Objectives

The simulated attacker attempts to achieve the following objectives:

| Objective | Purpose |
|-----------|---------|
| Identify current user | Determine the security context of the compromised session. |
| Identify the host | Obtain the computer name for situational awareness. |
| Enumerate network configuration | Discover IP addressing, interfaces, and network details. |
| Collect system information | Identify operating system version and hardware details. |
| Enumerate local users | Identify potential accounts for privilege escalation or lateral movement. |
| Enumerate running processes | Discover active applications, security tools, and potential targets. |

---

## Why This Matters

Native Windows utilities are frequently abused by attackers because they already exist on every Windows installation and generally do not require additional tools to be downloaded.

This behavior is commonly referred to as **Living off the Land (LotL)**, where legitimate operating system binaries are used to accomplish malicious objectives while blending into normal system activity.

Because the executed commands are legitimate administrative tools, defenders cannot rely solely on the executable name to determine malicious intent. Instead, analysts must evaluate the surrounding context, including:

- Parent-child process relationships
- Command execution sequence
- User context
- Frequency of execution
- Associated telemetry
- Overall attack timeline

This investigation demonstrates how seemingly benign command-line activity can become suspicious when analyzed collectively.

---

## Expected Telemetry

The execution of these commands is expected to generate endpoint telemetry through Sysmon.

Primary telemetry source:

| Telemetry Source | Event |
|------------------|-------|
| Sysmon | Event ID 1 – Process Create |

The generated events are forwarded to Splunk Enterprise through the Splunk Universal Forwarder, enabling the activity to be investigated from a SOC analyst's perspective.

---

## Investigation Goals

This investigation aims to answer the following questions:

- Were all executed commands successfully captured by Sysmon?
- Was the telemetry successfully forwarded to Splunk Enterprise?
- Can the reconnaissance activity be reconstructed from the available logs?
- Which telemetry fields provide the most valuable investigative context?
- How can analysts distinguish legitimate administrative activity from potentially malicious reconnaissance?

---

## Key Takeaways

> [!NOTE]
> Individual command-line utilities such as `whoami.exe` or `ipconfig.exe` are not inherently malicious. Their significance emerges when they are evaluated within the broader execution context and correlated with other reconnaissance activity.

---

## Next Step

➡ Continue to **[Attack Execution](attack-execution.md)**.