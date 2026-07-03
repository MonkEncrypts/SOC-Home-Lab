
# Field Dependency Matrix

| Field | Detection | Investigation | Reason |
|--------|-----------|---------------|--------|
| Event ID | ✅ | ✅ | Identifies Process Creation events |
| Image | ✅ | ✅ | Identifies the executable |
| CommandLine | ✅ | ✅ | Primary indicator of encoded execution |
| ParentImage | ❌ | ✅ | Establishes process lineage |
| ParentCommandLine | ❌ | ✅ | Provides additional context |
| User | ❌ | ✅ | Identifies the executing account |
| ProcessGuid | ❌ | ✅ | Correlates related Sysmon events |
| ParentProcessGuid | ❌ | ✅ | Correlates parent-child processes |
| IntegrityLevel | ❌ | ✅ | Indicates privilege level |
| Hashes | ❌ | ✅ | Verifies executable integrity |
| CurrentDirectory | ❌ | ✅ | Shows execution context |