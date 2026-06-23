# Sysmon Configuration Reference — SwiftOnSecurity Baseline

## Overview

This lab uses the SwiftOnSecurity Sysmon configuration (`sysmonconfig-export.xml`), a community-maintained baseline widely adopted across the security community as a sensible default for security monitoring. The configuration is not reproduced in full here (it is large and maintained upstream), but its key design principles are documented below for reference.

**Upstream source:** [SwiftOnSecurity/sysmon-config](https://github.com/SwiftOnSecurity/sysmon-config) on GitHub.

---

## Key Design Principles of the SwiftOnSecurity Configuration

### 1. Default-Include Philosophy for High-Value Events

Rather than logging everything (which would generate excessive noise and indexing volume) or logging nothing (the Sysmon default), the configuration takes a curated approach: it includes broad rules for high-signal event types and uses targeted excludes to filter out known-noisy, known-benign activity.

### 2. Process Creation (Event ID 1) Is Logged Broadly

Nearly all process creation is logged by default, since this is the single most valuable telemetry source for detection engineering. Specific high-noise, low-value processes (e.g., certain routine OS housekeeping processes) are excluded by name to control volume.

### 3. Network Connections (Event ID 3) Are Logged with Targeted Excludes

Network connection logging is enabled broadly but excludes well-known, high-volume, low-risk traffic patterns (e.g., certain local loopback or known Windows update endpoints) to avoid flooding the index with low-value network noise.

### 4. File Creation Time Changes (Event ID 2) Detect Timestomping

Sysmon's Event ID 2 specifically flags when a file's creation timestamp is modified — a known anti-forensic technique used to make malicious files blend in with legitimate, older files on disk.

### 5. Registry Modification (Event IDs 12/13/14) Focuses on Persistence Locations

Rather than logging all registry activity (extremely high volume), the configuration targets registry keys commonly abused for persistence, such as `Run` and `RunOnce` keys.

### 6. DNS Query Logging (Event ID 22) for C2 and Exfiltration Visibility

DNS query logging provides visibility into command-and-control beaconing and data exfiltration attempts that rely on DNS, which network connection logging alone may not fully capture (e.g., DNS tunneling).

### 7. Hashing Is Enabled for File-Based Detections

The configuration enables hash computation (SHA256 by default) for executed images, enabling correlation against known-malicious file hashes from threat intelligence feeds.

---

## Why This Matters for Detection Engineering

The choice of Sysmon configuration directly determines what detections are even *possible* to write later. A SOC analyst who understands *why* a given Sysmon configuration captures what it does — rather than treating it as an opaque file to drop in — is better equipped to:

- Diagnose why an expected detection isn't firing (the underlying event may be filtered out)
- Tune the configuration for an environment's specific noise profile
- Extend the configuration to cover a newly identified detection gap

This understanding directly informed the planned detection use cases in [Detection Use Cases](../docs/09-detection-usecases/detection-use-cases.md), each of which is mapped to a specific Sysmon Event ID that the SwiftOnSecurity configuration is known to reliably capture.
