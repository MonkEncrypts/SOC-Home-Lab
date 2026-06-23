# Future Improvements / Roadmap

This document tracks planned enhancements to the lab, organized by category. Items are intentionally specific and scoped so progress can be tracked clearly over time.

---

## Detection Engineering (Highest Priority)

- [ ] Run controlled attack simulations from the Kali Linux VM (starting with Atomic Red Team for safe, repeatable, well-documented techniques)
- [ ] Capture and validate Sysmon telemetry for each simulated technique
- [ ] Write and test Splunk detection searches mapped to MITRE ATT&CK for each of the seven planned use cases in [Detection Use Cases](../09-detection-usecases/detection-use-cases.md)
- [ ] Convert validated searches into saved Splunk alerts with appropriate severity and notification configuration
- [ ] Document at least one complete incident response walkthrough using the framework in [Incident Response Workflow](../10-incident-response/ir-playbook-template.md)

## Telemetry and Visibility

- [ ] Add Windows Security Event Log forwarding (logon/logoff, account management) alongside Sysmon for broader visibility
- [ ] Add a Splunk dashboard summarizing event volume by EventID, host, and time
- [ ] Add a third monitored endpoint (Ubuntu Server) to demonstrate cross-platform log normalization
- [ ] Forward Sysmon network connection events (Event ID 3) and correlate with a network-layer detection (see IDS item below)

## Network and Infrastructure

- [ ] Add Suricata or Zeek as a network IDS layer on the Host-Only network to provide network-based detection alongside endpoint-based detection
- [ ] Document the VMware Virtual Network Editor configuration with a screenshot (currently a documentation gap — see [Troubleshooting Log](../11-troubleshooting/troubleshooting-log.md))
- [ ] Evaluate migrating the lab to a dedicated pfSense or OPNsense virtual router for more realistic network segmentation and firewall logging

## Platform Comparison

- [ ] Stand up Elastic Stack (Elasticsearch + Kibana + Elastic Agent) as an alternative SIEM, ingesting the same Sysmon data, to document a side-by-side comparison of detection authoring experience between Splunk SPL and Elastic's query languages

## Documentation Quality

- [ ] See [Open Documentation Items](../11-troubleshooting/troubleshooting-log.md#open-documentation-items) for the current tracked list of outstanding screenshots and documentation
- [ ] Capture a genuine Event Viewer screenshot for Sysmon Verification — open Event Viewer → Applications and Services Logs → Microsoft → Windows → Sysmon → Operational on the Windows 10 endpoint; capture a view showing multiple Event IDs (1, 4, 5, 16) with timestamps visible, and ideally the details pane expanded on one Event ID 1 entry. Save as `sysmon-operational-events.png` in `docs/06-sysmon/screenshots/` and reference it in `sysmon-verification.md`.
- [ ] Add a `CONTRIBUTING.md` or lab-reproduction guide for others who want to build this same environment from scratch
