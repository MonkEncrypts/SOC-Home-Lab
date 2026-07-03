
# Telemetry Analysis

## Objective

Validate how Sysmon telemetry is stored and indexed within Splunk before developing detection logic.

## Observation

Investigation revealed that Sysmon events were indexed as raw XML using the following sourcetype:

```
XmlWinEventLog:Microsoft-Windows-Sysmon/Operational
```

Rather than automatically extracting fields such as `Image`, `CommandLine`, and `ParentImage`, Splunk stored the complete XML event inside the `_raw` field.

Example:

```xml
<Data Name='Image'>
C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe
</Data>
```

## Impact

Because XML fields were not automatically extracted:

- Searches using `EventCode=1` did not return results.
- Searches using `Image=` or `CommandLine=` were not supported.
- Initial investigations relied on searching raw XML content.

## Engineering Decision

Rather than modifying the ingestion pipeline during the first detection engineering case study, the existing telemetry format was retained. Planned improvements to field extraction and data normalization have been deferred to a future SIEM Engineering milestone to maintain focus on completing the Detection Engineering phase.