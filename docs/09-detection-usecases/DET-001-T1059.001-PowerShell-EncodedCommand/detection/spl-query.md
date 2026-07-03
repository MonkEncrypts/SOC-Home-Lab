
# SPL Detection Query

## Version 1

### Objective

Identify PowerShell executions using the `-EncodedCommand` parameter based on raw Sysmon XML telemetry.

### SPL Query

```spl
index=* "<EventID>1</EventID>" "EncodedCommand"
```

### Detection Logic

This query searches raw Sysmon Event ID 1 (Process Create) events for the presence of the string `EncodedCommand`.

At this stage of the project, Sysmon telemetry is stored as raw XML and important fields such as `Image` and `CommandLine` are not automatically extracted. Therefore, Version 1 performs direct string matching against the indexed XML.

### Advantages

- Simple
- Easy to validate
- Works with current telemetry format

### Limitations

- Relies on raw XML searches.
- Does not use extracted fields.
- May require refinement after telemetry normalization.
- Not optimized for production environments.
```