
# Lessons Learned

- Sysmon Event ID 1 records every process creation on the endpoint, not just suspicious activity. As a result, multiple Event ID 1 events may be generated within a very short period of time.

- During investigation, the most recent process creation event is not always the event of interest. Windows continues launching background processes, which can produce additional Event ID 1 logs immediately after the activity being investigated.

- Analysts should identify the correct process by correlating multiple fields such as `Image`, `CommandLine`, `ParentImage`, `User`, and the event timestamp instead of relying solely on the chronological order of events.

- Understanding the telemetry at the endpoint before searching the SIEM provides a stronger troubleshooting methodology. Verifying that Sysmon successfully generated the expected event helps isolate whether any issue exists on the endpoint, during log forwarding, or within the SIEM.

- Investigating the raw Sysmon event before writing detection logic provides a much deeper understanding of available telemetry and leads to more accurate and reliable detections.

## Investigation Note

During the investigation, the telemetry pipeline initially appeared to be failing because expected search queries returned no results. Systematic troubleshooting confirmed that Sysmon was generating events, the Splunk Universal Forwarder maintained an active connection to the Splunk server, and telemetry was being indexed successfully. The issue was caused by using search queries that assumed specific extracted fields, rather than first understanding how the data was stored in the SIEM.

**Key Lesson:** Always validate the ingestion pipeline before modifying configurations. Confirm endpoint telemetry, forwarding, indexing, and data structure before concluding that data collection has failed.