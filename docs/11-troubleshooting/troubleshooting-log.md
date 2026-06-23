# Troubleshooting Log

This document consolidates every significant technical issue encountered while building this lab, the investigation process used to diagnose it, the root cause, and the resolution. It is structured deliberately around the question set a SOC analyst is trained to ask during any investigation: *What was expected? What happened instead? How was the root cause isolated? How was it resolved and verified?*

---

## Issue 1 — Mobile Hotspot Client Isolation Blocking Inter-Device Communication

**Phase:** Initial multi-host architecture (pre-redesign)

### Expected Behavior
Two physical laptops connected to the same mobile hotspot network should be able to communicate directly with each other (e.g., via ICMP ping) in addition to reaching the internet.

### Problem Observed
Both laptops obtained valid IP addresses from the hotspot and could reach the internet normally. However, ICMP ping requests between the two laptops failed entirely — device-to-device communication did not work despite both devices being on the same subnet and reaching the same gateway successfully.

### Investigation
1. Reviewed Windows Firewall configuration on both machines — ruled out as the cause, since the block occurred at the network layer even with firewalls in default states.
2. Verified IP configuration and subnet membership on both devices — confirmed both were correctly addressed on the same `10.210.205.0/24` network.
3. Attempted a USB Wi-Fi adapter (Ralink chipset) configured as a Windows-hosted wireless network via `netsh wlan set hostednetwork` — the adapter reported hosted-network capability, but Windows 11 failed to reliably bring the hosted network online, likely due to a driver-level incompatibility with this specific adapter on Windows 11.
4. Attempted Internet Connection Sharing (ICS) as an alternative bridge — did not produce reliable peer-to-peer connectivity.

### Root Cause
The mobile hotspot enforced **client (AP) isolation** — a deliberate, common security feature on mobile and consumer access points that allows each connected client to reach the internet while blocking direct communication between clients. This is a hotspot-level control, not something fixable from either laptop.

### Resolution
Rather than acquiring additional networking hardware (e.g., a dedicated travel router without client isolation) to work around a third-party network's security control, the lab architecture was redesigned as a single-host virtualized environment using VMware Host-Only networking, which sidesteps the issue entirely by keeping all inter-VM traffic inside the hypervisor's own virtual switch. Full details in [Architecture Decisions](../02-architecture/architecture-decisions.md).

---

## Issue 2 — Kali VM Could Receive but Not Send ICMP Traffic to Windows Endpoint

**Phase:** Post-redesign, single-host architecture

### Expected Behavior
After migrating to VMware Host-Only networking, the Kali Linux attacker VM and the Windows 10 endpoint VM should have full bidirectional ICMP connectivity, since both are on the same `192.168.13.0/24` Host-Only network.

### Problem Observed
The Kali VM could successfully ping the Windows 10 endpoint. However, the Windows 10 endpoint could not ping Kali, returning:
```
PING: transmit failed. General failure.
```

### Investigation
IP configuration was reviewed on both VMs via VMware Workstation's network adapter settings and each guest's own IP configuration tools. This showed:
- Windows 10 Endpoint: `192.168.13.0/24` (Host-Only / VMnet1) — correct
- Kali Linux Attacker: `192.168.72.0/24` (NAT / VMnet8) — **not** the intended network

### Root Cause
The Kali VM's primary network adapter was set to **NAT mode** instead of **Host-Only mode** in VMware Workstation's VM settings. This created an asymmetric path: VMware's NAT implementation allowed Kali to reach the Host-Only network's gateway-adjacent traffic outbound, but the Windows endpoint had no return route back to Kali's NAT-assigned address.

### Resolution
The Kali VM's network adapter was reconfigured from NAT to Host-Only mode in **VM Settings → Network Adapter**. After this change, Kali received an address in the correct subnet (`192.168.13.129/24`), matching the Windows endpoint's network. Bidirectional ICMP connectivity was confirmed in both directions following the change.

---

## Issue 3 — Windows Endpoint Could Not Receive Pings from Splunk Host (Public Network Profile)

**Phase:** Post-redesign, single-host architecture

### Expected Behavior
With both systems on the same `192.168.13.0/24` Host-Only network, the Windows 11 Splunk host should be able to ping the Windows 10 endpoint, and vice versa.

### Problem Observed
The Windows 10 endpoint could successfully ping the Splunk host. The reverse direction failed — the Splunk host received request timeouts when pinging the endpoint.

### Investigation
1. Confirmed the VMware VMnet1 (Host-Only) adapter was active and correctly addressed on both systems.
2. Confirmed both systems were on the same `192.168.13.0/24` subnet with correct, non-conflicting addresses (`192.168.13.1` and `192.168.13.128`).
3. Reviewed the Windows 10 endpoint's network adapter classification using PowerShell and found the Host-Only adapter (`Ethernet0`) was categorized by Windows as an **Unidentified Public Network** rather than Private.

### Root Cause
Windows Defender Firewall applies different default rule sets depending on the active network profile (Domain, Private, or Public). The existing ICMP Echo Request allow-rules were scoped only to the Domain and Private profiles. Because the Host-Only adapter was classified as Public, inbound ICMP Echo Requests were silently dropped by the default Public-profile firewall policy.

### Resolution
A custom inbound Windows Defender Firewall rule was created on the Windows 10 endpoint, explicitly scoped to the Public profile:

| Parameter | Value |
|---|---|
| Rule Type | Custom |
| Program | All Programs |
| Protocol | ICMPv4 |
| ICMP Type | Echo Request |
| Action | Allow |
| Profile | Public |
| Scope | Any IP Address |

### Verification
After applying the rule, connectivity testing confirmed bidirectional success:
- Windows 10 Endpoint → Windows 11 Splunk Host: **Successful**
- Windows 11 Splunk Host → Windows 10 Endpoint: **Successful**

> **Note:** during this troubleshooting process, Windows Defender Firewall was temporarily disabled on the endpoint to isolate whether firewall filtering was the cause at all. Once the root cause (the profile-scoped ICMP rule gap) was confirmed, the firewall was re-enabled and the targeted rule above was applied — disabling the firewall entirely was a diagnostic step only, not the final fix.

---

## Issue 4 — Splunk Receiving Port Reachable Locally but Not from the Endpoint

**Phase:** Splunk receiver configuration

### Expected Behavior
After enabling Splunk's receiving port (TCP 9997) and confirming the service was listening, the Windows 10 endpoint should be able to establish a TCP connection to the Splunk host on that port.

### Problem Observed
A connectivity test from the Windows 10 endpoint failed:

```powershell
Test-NetConnection 192.168.13.1 -Port 9997
```
```
WARNING: TCP connect to (192.168.13.1 : 9997) failed
PingSucceeded    : True
TcpTestSucceeded : False
```

ICMP succeeded (confirming basic Layer 3 reachability), but the TCP handshake on port 9997 specifically failed.

### Investigation
On the Splunk host, `netstat` was used to confirm whether Splunk was actually bound to and listening on the expected port:

```cmd
netstat -ano | findstr :9997
```
```
TCP    0.0.0.0:9997    0.0.0.0:0    LISTENING
```

This confirmed Splunk's receiving service was active and correctly bound to all interfaces (`0.0.0.0`), ruling out a Splunk-side misconfiguration. Since the service was listening but the remote endpoint still couldn't connect, the Windows 11 host's firewall was the next suspect.

### Root Cause
Windows Defender Firewall on the Splunk host (Windows 11) had no inbound rule permitting external connections to TCP port 9997. Splunk could accept local loopback connections, but the firewall blocked the same port when accessed from another host on the network.

### Resolution
An inbound Windows Defender Firewall rule was created on the Splunk host:

| Parameter | Value |
|---|---|
| Rule Type | Port |
| Protocol | TCP |
| Local Port | 9997 |
| Action | Allow |
| Profiles | Domain, Private, Public |

### Verification
The connectivity test was re-run from the Windows 10 endpoint:

```powershell
Test-NetConnection 192.168.13.1 -Port 9997
```
```
TcpTestSucceeded : True
```

This confirmed the Windows 10 endpoint could now successfully establish a TCP session with the Splunk Enterprise receiver, clearing the way for the Universal Forwarder to connect. See supporting screenshot in [Splunk Receiver Configuration](../04-splunk-siem/splunk-receiver-config.md).

---

## Issue 5 — Incorrect VMware CD/DVD ISO Path Blocking Windows Boot

**Phase:** Windows 10 endpoint VM installation

### Expected Behavior
The newly created Windows 10 VM should boot from the mounted Windows 10 installation ISO and begin Windows Setup.

### Problem Observed
On first boot, the VM failed to load any installation media.

### Investigation
The VM's virtual CD/DVD (SATA) device settings were reviewed in VMware Workstation.

### Root Cause
The CD/DVD device was pointed at an incorrect or invalid ISO file path, so VMware had no valid boot media to present to the VM's virtual BIOS.

### Resolution
The ISO path was corrected under **VM Settings → CD/DVD (SATA)** to point to the correct Windows 10 installation ISO. The VM was then powered on again and successfully booted into Windows Setup.

---

## Summary Table

| # | Issue | Root Cause | Fix |
|---|---|---|---|
| 1 | Cross-device ping failure on mobile hotspot | Hotspot client (AP) isolation | Redesigned to single-host VMware architecture |
| 2 | Kali could ping Windows, not vice versa | Kali NIC set to NAT instead of Host-Only | Changed Kali adapter to Host-Only mode |
| 3 | Splunk host couldn't ping Windows endpoint | Endpoint's Host-Only adapter classified Public; ICMP rule scoped to Private/Domain only | Added Public-profile ICMPv4 inbound rule |
| 4 | TCP 9997 unreachable from endpoint despite Splunk listening | Windows 11 firewall blocked inbound TCP 9997 | Added inbound TCP 9997 firewall rule (all profiles) |
| 5 | Windows VM failed to boot from ISO | Incorrect CD/DVD ISO path in VM settings | Corrected ISO path in VM settings |

---

## Open Documentation Items

The following items are tracked as open work for this documentation set:

- **Sysmon Verification (Section 6):** No Event Viewer screenshot currently exists showing Sysmon Event IDs 1, 4, 5, and 16 in the GUI. Capture instructions are tracked in [Future Improvements](../13-future-improvements/roadmap.md).
- **Network Architecture:** No screenshot of the VMware Virtual Network Editor (`Edit → Virtual Network Editor`) showing the VMnet1/VMnet8 configuration exists. This would strengthen the network architecture section significantly.
- **Kali Linux Setup:** Configuration steps, installed tooling, and network verification for the Kali VM are not yet documented beyond the initial VM creation wizard screenshot. This is expected for the current pre-attack phase of the project.
- **Detection Engineering and Incident Response:** No attack simulations or validated detections have been completed yet — see [Detection Use Cases](../09-detection-usecases/detection-use-cases.md) and [Incident Response Workflow](../10-incident-response/ir-playbook-template.md) for current status. This is the intended next phase of work, not a gap in the completed infrastructure documentation.
