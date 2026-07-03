
# Detection Evolution

## Version 1

```spl
index=* "<EventID>1</EventID>" "EncodedCommand"
```

### Purpose

Validate that encoded PowerShell execution can be identified within raw Sysmon XML telemetry.

### Result

Successfully detected the simulated attack.

---

## Version 2

```spl
index=* "<EventID>1</EventID>" "powershell.exe" "EncodedCommand"
```

### Improvement

Adds the PowerShell executable as an additional search criterion, reducing unrelated Event ID 1 process creation events.

### Result

Provides a more focused detection while remaining compatible with the current XML-based telemetry.

---

## Future Improvements

After implementing improved Sysmon field extraction:

- Search using extracted fields instead of XML.
- Reduce false positives.
- Improve search performance.
- Normalize detection using Splunk CIM.
```