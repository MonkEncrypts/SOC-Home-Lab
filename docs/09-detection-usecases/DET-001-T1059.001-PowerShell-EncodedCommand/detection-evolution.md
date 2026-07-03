
# Detection Evolution

## Version 1
**Status:** ✅ Validated

### SPL Query

```spl
index=* "<EventID>1</EventID>" "EncodedCommand"
```

### Purpose

Validate that encoded PowerShell execution can be identified within raw Sysmon XML telemetry.

### Result

Successfully detected the simulated attack.

---

## Version 2
**Status:** ✅ Validated

### SPL Query

```spl
index=* "<EventID>1</EventID>" "powershell.exe" "EncodedCommand"
```

### Improvement

Adds the PowerShell executable as an additional search criterion, reducing unrelated Event ID 1 process creation events.

### Result

Provides a more focused detection while remaining compatible with the current XML-based telemetry.

---
## Engineering Decision

During the investigation, Sysmon telemetry was successfully ingested into Splunk. However, the XML event fields (such as `CommandLine`, `Image`, and `ParentImage`) were stored as raw XML rather than being extracted into searchable fields.

Because of this, searches such as:

```spl
CommandLine="*-EncodedCommand*"
```

were not supported by the existing ingestion pipeline.

Instead of interrupting the investigation to redesign the telemetry pipeline, the detection was first validated using raw XML searches.

Improving field extraction has been scheduled as a dedicated SIEM enhancement after DET-001 so that future detections can use native field-based searches while preserving the integrity of this investigation.

---
### Detection Strengths

- Successfully detects encoded PowerShell execution in the current telemetry format.
- Simple and easy to validate.
- Uses evidence collected during the investigation.
- Serves as a reliable baseline for future detection improvements.

### Limitations

- Relies on raw XML string matching instead of extracted Sysmon fields.
- Search performance may decrease as data volume increases.
- Does not currently identify all PowerShell execution variants.
- Requires future SIEM improvements to support field-based searches.
- Represents an initial validated detection rather than a production-ready analytic.

---
## Future Improvements

- Configure proper XML field extraction for Sysmon telemetry in Splunk.
- Replace raw XML string matching with searches using extracted fields such as `Image`, `CommandLine`, and `ParentImage`.
- Improve detection performance by reducing dependence on full-text XML searches.
- Expand the detection to include additional PowerShell execution patterns (for example `-enc` and `-e`).
- Evaluate the detection against additional benign PowerShell activity to further reduce false positives.

---
## Key Takeaway

- The objective of DET-001 was to validate the complete detection workflow—from attack execution to analyst investigation—not to optimize the SIEM ingestion pipeline. Pipeline improvements have therefore been intentionally scheduled as a separate engineering milestone.

- DET-001 successfully demonstrated the complete SOC investigation workflow, from attack execution and telemetry validation to detection development and analyst investigation. Future improvements will focus on enhancing the SIEM ingestion pipeline rather than changing the investigation methodology established during this case study.

---
