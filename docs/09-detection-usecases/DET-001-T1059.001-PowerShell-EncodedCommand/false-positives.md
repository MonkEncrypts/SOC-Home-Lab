# False Positive Analysis

## Observation

During the investigation, a search for `powershell.exe` returned legitimate PowerShell executions that were not related to the simulated attack.

One example was `splunk-powershell.exe`, which is executed by the Splunk Universal Forwarder during normal operation.

## Lesson Learned

Searching only for `powershell.exe` is not sufficient for a reliable detection because many legitimate Windows components and applications execute PowerShell.

Detection logic should instead focus on suspicious characteristics such as:

- Use of the `-EncodedCommand` parameter
- Suspicious parent-child process relationships
- Unusual command-line arguments
- User context
- Execution path
- Additional contextual telemetry

## Detection Improvement

Rather than detecting every PowerShell execution, this detection will specifically focus on identifying PowerShell processes executed with Base64-encoded command-line arguments.