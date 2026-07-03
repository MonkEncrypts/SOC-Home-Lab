
# Investigation Notes

## Investigation Timeline

### Step 1

Executed a Base64-encoded PowerShell command on the Windows endpoint.

### Step 2

Verified that Sysmon generated Event ID 1 in the Microsoft-Windows-Sysmon/Operational log.

### Step 3

Validated that the Splunk Universal Forwarder maintained an active connection to the Splunk Enterprise server.

### Step 4

Confirmed successful ingestion of Sysmon events into Splunk Enterprise.

### Step 5

Performed an initial search for `powershell.exe`.

### Step 6

Observed that the initial search returned both malicious test activity and legitimate PowerShell executions, including `splunk-powershell.exe`.

### Lesson

Detection engineering requires distinguishing malicious activity from legitimate administrative and application behavior. Initial searches should be treated as investigative steps rather than production-ready detections.