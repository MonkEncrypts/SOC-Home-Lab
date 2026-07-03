
# Analyst Investigation Playbook

## Alert

Suspicious PowerShell Encoded Command

---

## Investigation Objective

Determine whether the encoded PowerShell execution represents legitimate administrative activity or malicious execution.

---

## Investigation Checklist

### 1. Verify the Process

- Confirm that the process image is `powershell.exe`.
- Review the full command line.
- Verify that the `-EncodedCommand` parameter is present.

---

### 2. Identify the User

Determine:

- Which account executed the process?
- Is the account expected to execute PowerShell?

---

### 3. Review Parent Process

Investigate:

- ParentImage
- ParentCommandLine

Unexpected parent processes may indicate malicious execution.

---

### 4. Decode the Payload

Decode the Base64 command to determine:

- Script functionality
- Network activity
- File operations
- Persistence mechanisms

---

### 5. Review Additional Telemetry

Correlate:

- Network connections
- File creation
- Registry modifications
- Child processes

---

### 6. Determine Impact

Classify the activity as:

- Benign administrative usage
- Security testing
- Potential malicious execution

---

## Recommended Response

- Isolate the endpoint if malicious behavior is confirmed.
- Preserve forensic evidence.
- Review additional endpoint telemetry.
- Hunt for similar PowerShell executions across the environment.