# Lessons Learned

## Technical Lessons

### 1. Network Connectivity Should Be Validated Before Building Anything on Top of It

The most time-consuming issue in this project — mobile hotspot client isolation — was a Layer 3 connectivity problem that surfaced only after architecture and VM provisioning decisions had already been made around the original multi-host design. Validating basic ICMP connectivity between the two physical laptops *before* committing to that architecture would have surfaced the blocker earlier and saved rework. This reinforced a habit that applies directly to SOC and infrastructure work generally: confirm the foundational layer works before layering application-level configuration on top of it.

### 2. Firewall Profile Scoping Is a Common, Easy-to-Miss Source of "It Should Work" Failures

Two separate issues in this lab (Issues 3 and 4 in the [Troubleshooting Log](../11-troubleshooting/troubleshooting-log.md)) were ultimately caused by Windows Defender Firewall rules that existed but were scoped to the wrong network profile (Private/Domain instead of Public). Both times, the symptom looked like a routing or service problem at first glance. Methodically checking `netstat` for service-side listening status, and the network profile classification on the client side, was what isolated the actual cause each time — rather than assuming the firewall was simply "off" or "on."

### 3. A Working Architecture Is Not Always the First One Planned

The original two-laptop design was arguably a more "realistic" simulation of an enterprise network with physically separate systems. But it depended on a network (a mobile hotspot) that enforced a security control (client isolation) outside of this project's control. Recognizing when to abandon a technically valid plan in favor of one that's actually achievable with the available resources — without losing the educational value of the exercise — was an important judgment call.

### 4. Default Tool Configurations Are Rarely Sufficient for Security Monitoring

Both Sysmon and the Splunk Universal Forwarder ship with minimal default behavior. Sysmon's default configuration logs very little; the Universal Forwarder forwards nothing until explicitly told what to monitor via `inputs.conf`. Building real security visibility required deliberately selecting a community-vetted detection baseline (SwiftOnSecurity) and explicitly scoping the forwarding configuration — defaults are a starting point, not a destination, in security tooling.

---

## Process Lessons

### 5. Documentation Drift Is Real and Worth Catching Early

Several of this project's original notes described a planned file structure (`C:\SecurityTools\Sysmon\`) that didn't match what was actually executed (`C:\Users\SOCUser\Downloads\Sysmon`). Neither approach is wrong, but the mismatch between plan and execution is exactly the kind of detail that erodes trust in documentation if left uncorrected. Auditing screenshots and command output against written notes — rather than assuming the notes were followed exactly — surfaced this gap.

### 6. Screenshots Need the Same Quality Control as Written Documentation

During the audit of this repository, at least two screenshots were found to be exact duplicates placed in different sections under different names, while at least one section (Sysmon Verification via Event Viewer) was missing dedicated visual evidence entirely. Capturing a screenshot "at the time" is not the same as confirming, later, that it actually supports the specific claim made next to it.

### 7. A Build Log Is More Valuable Than a Clean Narrative

Keeping the original troubleshooting notes — including the dead-end USB Wi-Fi adapter attempt and the failed ICS configuration — rather than deleting them in favor of a clean "and then it worked" narrative produced a far more useful document. It demonstrates the actual diagnostic process, which is what a SOC analyst is evaluated on far more than the final answer alone.

---

## What This Project Demonstrates for a SOC Analyst Role

- **Methodical troubleshooting:** every issue in this lab was investigated using the same expected-behavior → observed-problem → root-cause → resolution → verification structure that SOC incident documentation follows.
- **Tool fluency:** hands-on configuration of a SIEM (Splunk), an endpoint telemetry agent (Sysmon), and a log-shipping agent (Splunk UF) — the same category of tools used daily in production SOC environments.
- **Network fundamentals:** practical experience with IP addressing, network segmentation, NAT vs. Host-Only networking, and Windows Firewall profile behavior.
- **Detection-oriented thinking:** explicit awareness that the goal of telemetry collection is to support detection engineering, reflected in the choice of the SwiftOnSecurity Sysmon baseline and the planned MITRE ATT&CK-mapped detection use cases.
- **Adaptability under real constraints:** willingness to redesign an architecture when the original plan proved impractical, rather than persisting with a non-working approach.
