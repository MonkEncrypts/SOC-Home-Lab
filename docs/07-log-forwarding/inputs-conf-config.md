# inputs.conf — Sysmon Log Forwarding Configuration

## Objective

Configure the Splunk Universal Forwarder to monitor and forward only the Sysmon Operational event log channel, rather than the full set of default Windows Event Log channels.

## Why Scope the Forwarder to Sysmon Only

By default, a Splunk Universal Forwarder can be configured to forward multiple Windows Event Log channels (System, Security, Application, etc.). For this lab, the forwarder was deliberately scoped to forward **only** the Sysmon Operational channel for two reasons:

1. **Signal-to-noise ratio.** Sysmon (configured with the SwiftOnSecurity baseline) is purpose-built to capture high-value security telemetry. Forwarding the default Windows logs alongside it at this stage would add indexing volume without adding proportional detection value, and would complicate validating that the Sysmon pipeline specifically was working end-to-end.
2. **Free license indexing limits.** Splunk Enterprise's free license caps daily indexing volume at 500 MB. Scoping ingestion to the most valuable log source first is a practical resource-management decision, consistent with how a SOC team would prioritize onboarding high-value log sources before lower-priority ones.

---

## Configuration File

The forwarding configuration lives in `inputs.conf`, located at:

```
C:\Program Files\SplunkUniversalForwarder\etc\system\local\inputs.conf
```

### Configuration Content

```ini
[WinEventLog://Microsoft-Windows-Sysmon/Operational]
disabled = false
index = main
sourcetype = WinEventLog:Microsoft-Windows-Sysmon/Operational
```

| Directive | Purpose |
|---|---|
| `[WinEventLog://Microsoft-Windows-Sysmon/Operational]` | Stanza header specifying the exact Windows Event Log channel to monitor — the same channel verified manually in Event Viewer during [Sysmon Verification](../06-sysmon/sysmon-verification.md) |
| `disabled = false` | Explicitly enables this input (inputs are disabled by default until declared) |
| `index = main` | Sends matched events into Splunk's default `main` index |
| `sourcetype = WinEventLog:Microsoft-Windows-Sysmon/Operational` | Assigns a sourcetype that lets Splunk correctly parse and field-extract the Windows Event Log XML structure |

A copy of this configuration file is preserved in the repository at [`configs/inputs.conf`](../../configs/inputs.conf) for reference and reproducibility.

---

## Applying the Configuration

After editing `inputs.conf`, the Universal Forwarder service was restarted to apply the new monitoring configuration:

```cmd
net stop SplunkForwarder
net start SplunkForwarder
```

## Verification

Successful application of this configuration is confirmed in [Log Verification](../08-log-verification/splunk-search-validation.md), where Sysmon Event ID 1 (Process Creation) events are shown arriving in Splunk under the expected `sourcetype`.
