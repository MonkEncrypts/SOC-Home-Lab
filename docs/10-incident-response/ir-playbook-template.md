# Incident Response Workflow — Template

## Status: Planned

This lab's detection content is still in development (see [Detection Use Cases](../09-detection-usecases/detection-use-cases.md)). Once at least one detection use case is validated end-to-end, this document will be expanded into a full, evidenced incident response walkthrough — from alert triggering, through investigation, to documented closure.

This page currently defines the IR framework that will be followed, so the structure is in place ahead of the content.

---

## Planned IR Workflow Structure

### 1. Detection / Alert
- Which Splunk search or correlation rule fired
- Severity classification
- Initial alert context (host, user, time, technique)

### 2. Triage
- Is this a true positive, false positive, or benign-but-suspicious activity?
- What is the scope — single host, single user, or broader?

### 3. Investigation
- Pivoting through related Sysmon events (process tree reconstruction via `ParentProcessGuid`/`ProcessGuid`)
- Correlating process creation (Event ID 1), network connections (Event ID 3), and file/registry activity (Event IDs 11/12/13)
- Timeline reconstruction

### 4. Containment (Simulated)
- What containment action would be taken in a real environment (isolate host, disable account, block IOC)
- Since this is an isolated lab network, containment steps will be documented as the action that *would* be taken in production, clearly labeled as simulated

### 5. Root Cause and Impact Assessment
- What allowed the activity to occur
- What was or could have been affected

### 6. Remediation
- Steps taken to remove the threat and prevent recurrence

### 7. Lessons Learned / Detection Improvement
- Was the detection logic sufficient?
- What new detection or hardening step should be added as a result?

---

## Next Steps

This page will be populated with a real, evidenced walkthrough once the first attack simulation (via the Kali Linux VM) and its corresponding Splunk detection are completed. See [Detection Use Cases](../09-detection-usecases/detection-use-cases.md) for current progress on that prerequisite work.
