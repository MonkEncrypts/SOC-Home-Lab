
# Detection Validation

## Validation Result

**Status:** ✅ Successful

The detection hypothesis was successfully validated.

A PowerShell process was executed using the `-EncodedCommand` parameter on the Windows endpoint.

Sysmon generated Event ID 1 (Process Create), and the event was successfully forwarded to Splunk Enterprise through the Splunk Universal Forwarder.

The resulting telemetry contained all of the information required for investigation and detection.

## Evidence Observed

- Event ID: 1 (Process Create)
- Process Image: `powershell.exe`
- Parent Process: `cmd.exe`
- Command Line contained the `-EncodedCommand` parameter
- User context successfully recorded
- Integrity Level recorded
- Process GUID recorded
- Parent Process GUID recorded
- Process hashes recorded

## Detection Outcome

The initial SPL query successfully identified the simulated PowerShell execution using raw Sysmon XML telemetry.

The detection hypothesis is considered validated.