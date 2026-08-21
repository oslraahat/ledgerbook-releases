## `Release Workflow`

```text
Release Tag (v*)
        │
        ├────────────────┬────────────────┬────────────────┐
        ▼                ▼                ▼                ▼
┌───────────────┐ ┌───────────────┐ ┌───────────────┐ ┌───────────────┐
│ 1. Secrets    │ │ 2. Version    │ │ 3. Release    │ │ 4. Gradle     │
│               │ │               │ │    Notes      │ │    Wrapper    │
│ Validate      │ │ Tag format    │ │ File exists   │ │ Validate      │
│ required      │ │ versionName   │ │ & not empty   │ │ wrapper       │
│ secrets       │ │ versionCode   │ │               │ │ integrity     │
└───────┬───────┘ └───────┬───────┘ └───────────────┘ └───────┬───────┘
        │                  │                                   │
        ▼                  │                                   │
┌───────────────┐          │                                   │
│ 5. Repository │          │                                   │
│               │          │                                   │
│ Resolve       │          │                                   │
│ destination   │          │                                   │
│ Check duplicate          │                                   │
└───────┬───────┘          │                                   │
        │                  │                                   │
        │                  └────────────────┬──────────────────┘
        │                                   │
        │                  ┌────────────────┴────────────────┐
        │                  ▼                                 ▼
        │          ┌────────────────┐               ┌────────────────┐
        │          │ 6. Unit Tests  │               │ 7. Android     │
        │          │                │               │    Lint        │
        │          │ testDebug      │               │ lintRelease    │
        │          │ UnitTest       │               │ + Signing      │
        │          └───────┬────────┘               └───────┬────────┘
        │                  │                                │
        └──────────────────┴────────────────┬───────────────┘
                                           │
                                           ▼
                                ┌───────────────────────┐
                                │ 8. Build Signed APK   │
                                │                       │
                                │ assembleRelease       │
                                │ SHA-256               │
                                │ APK Artifact          │
                                │ R8 Mapping            │
                                └───────────┬───────────┘
                                            │
                                            ▼
                                ┌───────────────────────┐
                                │ 9. Inspect APK        │
                                │                       │
                                │ SHA-256               │
                                │ Signature             │
                                │ Certificate           │
                                │ Version               │
                                │ Version Code          │
                                │ Debuggable Check      │
                                └───────────┬───────────┘
                                            │
                                            ▼
                                ┌───────────────────────┐
                                │ 10. Publish Release   │
                                │                       │
                                │ GitHub Release        │
                                │ APK                   │
                                │ SHA-256               │
                                │ Release Notes         │
                                └───────────┬───────────┘
                                            │
                                            ▼
                                ┌───────────────────────┐
                                │ Published APK         │
                                │ Verification           │
                                │                       │
                                │ Download APK          │
                                │ Verify SHA-256        │
                                └───────────┬───────────┘
                                            │
                                            ▼
                                         ✅ Released
```

**Release flow:** `Tag → Validate → Test → Lint → Build → Verify → Publish → Verify Again`
