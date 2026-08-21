## `Release Workflow`

```text
Release Tag (v*)
        │
        ├──────────────────┬──────────────────┬──────────────────┐
        ▼                  ▼                  ▼                  ▼
┌───────────────┐  ┌───────────────┐  ┌───────────────┐  ┌───────────────┐
│ 1. Secrets    │  │ 2. Version    │  │ 3. Notes      │  │ 4. Wrapper    │
│ Validate      │  │ Validate      │  │ Validate      │  │ Validate      │
│ secrets       │  │ tag/version   │  │ release notes │  │ Gradle wrapper│
└───────┬───────┘  └───────┬───────┘  └───────────────┘  └───────┬───────┘
        │                   │                                     │
        ▼                   │                                     │
┌───────────────┐           │                                     │
│ 5. Repository │           │                                     │
│ Resolve repo  │           │                                     │
│ Check release │           │                                     │
└───────┬───────┘           │                                     │
        │                   │                                     │
        └───────────────────┼─────────────────────────────────────┘
                            │
                   ┌────────┴────────┐
                   ▼                 ▼
          ┌────────────────┐  ┌────────────────┐
          │ 6. Unit Tests  │  │ 7. Android Lint│
          │ testDebug      │  │ lintRelease    │
          └────────┬───────┘  └────────┬───────┘
                   │                   │
                   └─────────┬─────────┘
                             │
                             ▼
                  ┌──────────────────────┐
                  │ 8. Signed APK Build  │
                  │ • assembleRelease    │
                  │ • SHA-256            │
                  │ • APK Artifact       │
                  │ • R8 Mapping         │
                  └──────────┬───────────┘
                             │
                             ▼
                  ┌──────────────────────┐
                  │ 9. APK Inspection    │
                  │ • SHA-256            │
                  │ • Signature          │
                  │ • Certificate        │
                  │ • Version            │
                  │ • Version Code       │
                  │ • Debuggable Check   │
                  └──────────┬───────────┘
                             │
                             ▼
                  ┌──────────────────────┐
                  │ 10. Publish Release  │
                  │ • APK                │
                  │ • SHA-256            │
                  │ • Release Notes      │
                  └──────────┬───────────┘
                             │
                             ▼
                  ┌──────────────────────┐
                  │ Published APK        │
                  │ Verification         │
                  │ • Download APK       │
                  │ • Verify SHA-256     │
                  └──────────┬───────────┘
                             │
                             ▼
                          ✅ Released
```

**Release flow:** `Tag → Validate → Test → Lint → Build → Verify → Publish → Verify Again`
