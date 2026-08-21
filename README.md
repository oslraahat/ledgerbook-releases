## `LedgerBook Release Workflow Structure`

```text
Release Tag (v*)
        │
        ▼
┌─────────────────────────────────────┐
│ Job 1 · Preflight & Validate        │
├─────────────────────────────────────┤
│ 01. Checkout source code            │
│ 02. Validate required secrets       │
│ 03. Validate release version        │
│     ├─ Tag format                   │
│     ├─ versionName == tag           │
│     └─ versionCode monotonicity     │
│ 04. Validate release notes          │
│ 05. Resolve releases repository     │
│ 06. Duplicate release check         │
└──────────────────┬──────────────────┘
                   │
                   ▼
┌─────────────────────────────────────┐
│ Job 2 · Quality Checks              │
├─────────────────────────────────────┤
│ 07. Setup Java 17                   │
│ 08. Validate Gradle wrapper         │
│ 09. Setup Gradle                    │
│ 10. Prepare Gradle wrapper          │
│ 11. Run Android Lint                │
│ 12. Run unit tests                  │
│ 13. Upload test/lint reports        │
└──────────────────┬──────────────────┘
                   │
                   ▼
┌─────────────────────────────────────┐
│ Job 3 · Build & Verify              │
├─────────────────────────────────────┤
│ 14. Restore signing keystore        │
│     └─ Write keystore.properties    │
│ 15. Build signed release APK        │
│ 16. Verify APK exists               │
│ 17. Verify APK signature            │
│ 18. Verify signing certificate      │
│     └─ SHA-256 fingerprint          │
│ 19. Verify APK version metadata     │
│     ├─ versionName                  │
│     ├─ versionCode                  │
│     └─ debuggable = false           │
│ 20. Calculate APK SHA-256           │
│ 21. Upload verified APK             │
│ 22. Upload R8 mapping/symbols       │
│ 23. Shred signing material          │
└──────────────────┬──────────────────┘
                   │
                   ▼
┌─────────────────────────────────────┐
│ Job 4 · Package                     │
├─────────────────────────────────────┤
│ 24. Archive R8 mapping & symbols    │
│ 25. Package release artifact        │
│     ├─ Rename APK                   │
│     ├─ Generate .sha256             │
│     ├─ Verify SHA-256               │
│     └─ Compare build hash           │
│ 26. Upload release artifact         │
└──────────────────┬──────────────────┘
                   │
                   ▼
┌─────────────────────────────────────┐
│ Job 5 · Publish                     │
├─────────────────────────────────────┤
│ 27. Publish release                 │
│     └─ Public releases repository   │
│ 28. Download published APK          │
│ 29. Verify published APK exists     │
│ 30. Verify published APK SHA-256    │
│ 31. Write GitHub release summary    │
└─────────────────────────────────────┘
```

Publish / Published APK Verification Failed
      ↓
RELEASE NOT CONFIRMED
```
