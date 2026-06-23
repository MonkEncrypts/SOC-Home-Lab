# Architecture Decisions and Migration History

## Purpose of This Document

This document records the architectural evolution of the lab — what was originally planned, what went wrong, and why the final design was chosen. Preserving this history is intentional: real SOC and infrastructure work rarely goes according to the first plan, and documenting the decision-making process is itself a useful skill to demonstrate to employers.

---

## Phase 1 — Initial Multi-Host Design

### Plan

The lab was originally designed as a two-physical-machine environment to most closely simulate a real enterprise network, where the attacker and the monitored endpoint exist on physically separate systems:

- **Attacker Machine:** Kali Linux VM running on a personal laptop (Lenovo)
- **Monitoring Server:** Splunk Enterprise running on the Windows 11 host (ASUS)
- **Endpoint System:** Windows 10 VM running on the same Windows 11 host

Both physical laptops were intended to communicate over a shared mobile hotspot, with VMware Bridged networking extending that connectivity into each VM.

### Problem — Mobile Hotspot Client Isolation

Both laptops successfully connected to the hotspot and received valid IP addresses on the `10.210.205.0/24` network. Internet access worked normally from both machines. However, direct device-to-device communication failed completely:

- ICMP ping requests between the two laptops timed out
- Both devices could reach the gateway and the internet
- Neither device could reach the other directly

### Investigation

The following troubleshooting steps were performed, in order:

1. **Windows Firewall review** — confirmed ICMP and relevant rules were not the cause
2. **IP configuration analysis** — confirmed both devices held valid, correctly-scoped addresses on the same subnet
3. **USB Wi-Fi adapter (Ralink) test** — attempted to create an independent wireless network using `netsh wlan set hostednetwork`. The adapter reported hosted network support, but Windows 11 failed to reliably initialize the hosted network, likely due to driver-level limitations with this specific chipset on Windows 11.
4. **Internet Connection Sharing (ICS)** — attempted as an alternative method of bridging connectivity between adapters; did not produce reliable peer connectivity.

### Root Cause

The mobile hotspot enforced **client isolation** (also known as AP isolation) — a standard feature on consumer and mobile hotspot access points that prevents connected clients from communicating directly with one another while still allowing each client to reach the internet. This is a deliberate security feature on the hotspot itself, not a misconfiguration on either laptop, and it cannot be disabled from the client side.

---

## Phase 2 — Architecture Redesign Decision

Rather than continuing to invest time working around a network control that could not be disabled without specialized hardware (e.g., a dedicated travel router), the decision was made to redesign the lab as a **single-host virtualized environment**.

### Why This Was the Right Call

- It removed dependence on external networking hardware entirely.
- VMware Host-Only networking provides full Layer 2/3 connectivity between VMs with no client isolation concerns, since the "network" is a software-defined virtual switch controlled by the hypervisor.
- It simplified troubleshooting — all components live on one physical machine, removing an entire class of physical-network variables.
- It is more reproducible: anyone cloning this lab only needs one machine with sufficient resources, not two networked devices.

### Final Architecture

| Component | Host |
|---|---|
| Splunk Enterprise (SIEM) | Windows 11 physical host |
| VMware Workstation Pro | Windows 11 physical host |
| Windows 10 Endpoint VM | Hosted in VMware on the same machine |
| Kali Linux Attacker VM | Hosted in VMware on the same machine |
| Internal connectivity | VMware Host-Only network (VMnet1) |

---

## Lessons From This Decision

Building a security lab — like building real infrastructure — requires adapting plans to environmental constraints rather than forcing an original design to work at all costs. The original multi-host design was technically sound and arguably more "realistic," but the available networking hardware made it impractical within a reasonable timeframe.

This experience reinforced three habits directly relevant to SOC work:

1. **Validate assumptions early.** Confirming basic Layer 3 connectivity (ping) before building anything on top of it would have surfaced the hotspot isolation issue sooner.
2. **Document the "why," not just the "what."** A future engineer (or an interviewer) benefits far more from understanding *why* the architecture changed than from a description of the final state alone.
3. **Know when to cut losses on a troubleshooting path.** Continuing to chase a USB Wi-Fi adapter workaround past the point of diminishing returns would have delayed the project without addressing the root cause.
