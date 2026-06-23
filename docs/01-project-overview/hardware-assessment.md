# Hardware Assessment and Architecture Planning

## Objective

Evaluate the available physical hardware and select an appropriate SOC home lab architecture that balances realism with the constraints of consumer-grade equipment.

---

## Available Hardware

### System 1 — Attacker Machine

| Attribute | Detail |
|---|---|
| Device Model | Lenovo 81NB |
| Processor | AMD Ryzen 5 3500U (4 Cores / 8 Threads) |
| RAM | 8 GB DDR4 |
| Storage | 256 GB SSD + 1 TB HDD |
| Assigned Role | Kali Linux Attacker VM |

### System 2 — SOC Host Machine

| Attribute | Detail |
|---|---|
| Device Model | ASUS Vivobook M1502YA |
| Processor | AMD Ryzen 7 5825U (8 Cores / 16 Threads) |
| RAM | 16 GB DDR4 |
| Storage | 512 GB NVMe SSD |
| Assigned Role | Splunk SIEM Host + Windows 10 Endpoint VM |

---

## Architecture Decision Rationale

The ASUS Vivobook was selected as the primary SOC host for the following reasons:

- The AMD Ryzen 7 5825U provides 8 physical cores with 16 threads, offering sufficient headroom to run Splunk Enterprise alongside one or more virtual machines simultaneously.
- 16 GB of RAM allows Windows 10 VM allocation (4 GB) while leaving adequate memory for the host OS and Splunk processes.
- The 512 GB NVMe SSD provides low-latency I/O, which is important for Splunk index writes under continuous log ingestion.

Splunk Enterprise was deployed directly on the Windows 11 host operating system rather than inside a dedicated VM. This decision was made to maximize the resources available to the virtual machines and avoid the additional memory overhead that a Linux SIEM VM would require.

The Lenovo laptop was assigned to the attacker role because its hardware profile is sufficient for Kali Linux and network-based attack tooling, which are generally less resource-intensive than a SIEM platform.

---

## Initial Architecture Plan

The original design intended to use both physical machines communicating over a shared Wi-Fi network:

```
Lenovo Laptop (Kali Attacker)  ←──── Mobile Hotspot ────→  ASUS Laptop (Windows 10 VM + Splunk)
```

This multi-device design most closely mirrors a real-world environment where the attacker and victim machines reside on separate physical systems.

---

## Architecture Change — Single Host Design

During initial implementation, the mobile hotspot used as the shared network enforced **client isolation**, a common security feature in mobile network access points that prevents connected devices from communicating directly with each other. Despite both devices receiving valid IP addresses from the hotspot, peer-to-peer traffic was blocked at the network layer.

Troubleshooting steps attempted before the redesign decision:

- VMware Bridged networking (both devices)
- Windows Defender Firewall rule adjustments
- USB Wi-Fi adapter (Ralink) with hosted network mode via `netsh`
- Internet Connection Sharing (ICS) between adapters

None of these reliably resolved the isolation issue without additional hardware. Rather than introducing further complexity, the architecture was redesigned as a **single-host virtualized environment**.

The final architecture runs all components on the ASUS Vivobook using VMware Workstation Pro, with VMware Host-Only networking providing an isolated internal network between the virtual machines.

### Final Architecture Summary

| Component | Location | Notes |
|---|---|---|
| Splunk Enterprise (SIEM) | Windows 11 Host OS | Direct installation, no VM overhead |
| Windows 10 Endpoint VM | VMware on ASUS Host | Victim machine, Sysmon + UF installed |
| Kali Linux Attacker VM | VMware on ASUS Host | Attack simulation platform |
| Internal Network | VMware Host-Only (VMnet1) | Isolated — 192.168.13.0/24 |
| Internet Access | VMware NAT (VMnet8) | Updates and tool downloads only |

This architecture provides a fully contained, reproducible SOC lab environment that accurately simulates the telemetry flow and detection workflows used in enterprise security operations.
