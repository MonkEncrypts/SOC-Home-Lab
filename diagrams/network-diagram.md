# Network Diagram

## ASCII Reference Diagram

```
                          ┌──────────────────────────────────────┐
                          │         Windows 11 Host Machine      │
                          │         ASUS Vivobook M1502YA        │
                          │                                      │
                          │  ┌────────────────────────────────┐  │
                          │  │   Splunk Enterprise (SIEM)     │  │
                          │  │   Listening on TCP 9997        │  │
                          │  └───────────────┬────────────────┘  │
                          └──────────────────┼───────────────────┘
                                             │
                              ───────────────────────────────
                              VMnet1 — VMware Host-Only Network
                                    192.168.13.0/24
                                    Gateway: 192.168.13.1
                              ───────────────────────────────
                                             │
                      ┌──────────────────────┴──────────────────────┐
                      │                                             │
          ┌───────────┴──────────────┐              ┌──────────────┴────────────┐
          │   Windows 10 Endpoint   │              │   Kali Linux Attacker     │
          │   192.168.13.128/24     │              │   192.168.13.129/24       │
          │                         │              │                            │
          │   Sysmon + UF running   │              │   Attack tools / Kali     │
          └───────────┬─────────────┘              └──────────────┬────────────┘
                      │                                           │
                      └───────────────── VMnet8 ─────────────────┘
                                   VMware NAT Network
                                    192.168.72.0/24
                                         │
                                    Internet Access
                              (Updates, tool downloads)
```

## Mermaid Source

The following Mermaid diagram source renders the same architecture and can be pasted into any Mermaid-compatible renderer (GitHub natively renders Mermaid in `.md` files):

```mermaid
graph TD
    subgraph Host["Windows 11 Host Machine — ASUS Vivobook M1502YA"]
        Splunk["Splunk Enterprise SIEM<br/>192.168.13.1<br/>TCP 9997"]
    end

    subgraph VMnet1["VMnet1 — Host-Only Network (192.168.13.0/24)"]
        Splunk
        Win10["Windows 10 Endpoint<br/>192.168.13.128<br/>Sysmon + Universal Forwarder"]
        Kali["Kali Linux Attacker<br/>192.168.13.129"]
    end

    subgraph VMnet8["VMnet8 — NAT Network (192.168.72.0/24)"]
        Internet["Internet Access<br/>Updates / Tool Downloads"]
    end

    Win10 -- "TCP 9997 (Sysmon telemetry)" --> Splunk
    Win10 -.-> Internet
    Kali -.-> Internet
    Kali -. "Future: attack simulation" .-> Win10
```

## Log Flow Diagram

```mermaid
sequenceDiagram
    participant S as Sysmon (Windows 10)
    participant UF as Splunk Universal Forwarder
    participant SP as Splunk Enterprise (SIEM)

    S->>S: Generate telemetry (Event ID 1, 3, 5, 11, 12, 13...)
    S->>UF: Write to Microsoft-Windows-Sysmon/Operational
    UF->>UF: Read channel per inputs.conf
    UF->>SP: Forward over TCP 9997
    SP->>SP: Index event (sourcetype=WinEventLog:...Sysmon/Operational)
    SP->>SP: Available for search, alerting, detection
```
