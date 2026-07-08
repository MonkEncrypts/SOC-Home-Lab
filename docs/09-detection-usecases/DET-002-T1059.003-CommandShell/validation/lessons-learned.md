# Lessons Learned
| Previous | Current | Next |
|----------|---------|------|
| [← Validation](validation.md) | **Lessons Learned** | [Return to README →](../README.md) |

---

> [!NOTE]
>
> **Document:** Lessons Learned
>
> **Investigation Phase:** 10 of 10
>
> **Detection ID:** DET-002
>
> **Status:** ✅ Completed

---

# Purpose

Every completed investigation provides an opportunity to improve both technical knowledge and investigative methodology.

The lessons documented here extend beyond the specific detection and will be applied to future investigations within the SOC Home Lab. Capturing these observations helps establish a repeatable engineering process while continuously improving the quality of documentation, analysis, and detection development.

---

# Technical Lessons

This investigation reinforced several important technical concepts related to Windows telemetry and endpoint visibility.

- Sysmon Event ID 1 provides rich process creation telemetry that is highly valuable during endpoint investigations.
- Native Windows command-line utilities generate sufficient telemetry to reconstruct attacker activity when appropriate logging is enabled.
- Parent-child process relationships provide essential context for understanding how processes were launched.
- Even when structured field extraction is unavailable, raw XML telemetry remains a reliable source for manual investigation.

---

# Investigation Lessons

This investigation improved several practical SOC investigation skills.

- Investigations should begin with broad searches before progressively narrowing the scope.
- Evidence should be validated at every stage of the telemetry pipeline before drawing conclusions.
- Individual Windows commands should never be evaluated in isolation; surrounding context is significantly more important.
- Maintaining an evidence-driven investigation improves both accuracy and confidence in analyst findings.

---

# Detection Engineering Lessons

Developing effective detections requires more than identifying individual executables.

This investigation demonstrated the importance of:

- Detecting behavioural patterns rather than isolated commands.
- Considering execution sequences instead of individual events.
- Evaluating parent-child process relationships.
- Balancing detection coverage with false positive reduction.
- Designing detections that provide meaningful investigative context.

---

# Documentation Lessons

One of the most valuable outcomes of this investigation was the improvement of the documentation process itself.

Compared to previous investigations, the documentation for DET-002 was developed alongside the investigation rather than after its completion. This approach ensured that technical observations, screenshots, and analyst reasoning were captured while the investigation was actively being performed.

Separating the investigation into focused documents also improved readability and created a clearer progression from attack simulation through evidence collection, investigation, detection logic, validation, and final conclusions.

This documentation methodology will become the standard for future detections within the SOC Home Lab.

---

# Future Improvements

Several enhancements have been identified for future investigations.

- Implement structured Sysmon XML field extraction within Splunk to simplify investigations.
- Expand behavioural detections by correlating multiple related ATT&CK techniques.
- Continue improving investigation templates to maintain consistency across the SOC Home Lab.
- Introduce additional detection scenarios covering lateral movement, persistence, credential access, and defense evasion.

These improvements have been intentionally deferred to maintain project momentum while continuing to build practical SOC investigation experience.

---

# Final Reflection

This investigation demonstrated that effective SOC analysis extends beyond identifying individual events.

The true value of an investigation lies in correlating telemetry, understanding attacker behaviour, validating evidence, and communicating findings through clear and structured documentation.

Beyond the technical aspects of Windows command-line reconnaissance, this investigation also established a more mature documentation workflow that will be reused throughout the remainder of the SOC Home Lab project.

---

## Interview Insight

> **Interview Insight**
>
> One of the biggest lessons from this investigation is that SOC analysts investigate behaviour, not binaries.
>
> A command such as `whoami.exe` is rarely suspicious on its own. However, when executed alongside host discovery, network enumeration, process enumeration, and account discovery commands within a short time frame, it may indicate the reconnaissance phase of an intrusion.
>
> This investigation reinforced the importance of correlating evidence, validating telemetry, and making decisions based on context rather than isolated events.

---

## Next Step

➡ Return to the **[README](../README.md)** to review the complete investigation and navigate to the supporting documentation.