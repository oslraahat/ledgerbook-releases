## `LedgerBook Release Workflow Structure`

```text
Version Tag (v*)
        │
        ▼
┌──────────────────────────────────────┐
│ 1. Secrets                           │
├──────────────────────────────────────┤
│ • Validate required secrets          │
│ • KEYSTORE_BASE64                    │
│ • Store Password                     │
│ • Key Alias                          │
│ • Key Password                       │
│ • Releases Repo Token                │
└──────────────────────────────────────┘
        │
        ├─────────────────────────────────────────────┐
        │                                             │
        ▼                                             ▼
┌──────────────────────────────┐       ┌──────────────────────────────┐
│ 2. Version                   │       │ 3. Release Notes             │
├──────────────────────────────┤       ├──────────────────────────────┤
│ • Checkout source            │       │ • Checkout source             │
│ • Validate tag format        │       │ • Find release-notes file     │
│ • Validate versionName       │       │ • Validate file exists        │
│ • Validate versionCode       │       │ • Validate file is not empty  │
│ • Compare previous version   │       └──────────────────────────────┘
│ • Detect pre-release         │
│ • Validate default branch   │
└──────────────────────────────┘
        │
        ├─────────────────────────────────────────────┐
        │                                             │
        ▼                                             ▼
┌──────────────────────────────┐       ┌──────────────────────────────┐
│ 4. Gradle Wrapper            │       │ 5. Releases Repository        │
├──────────────────────────────┤       ├──────────────────────────────┤
│ • Checkout source            │       │ • Depends on Secrets          │
│ • Validate Gradle wrapper    │       │ • Checkout source              │
│ • Verify wrapper integrity   │       │ • Resolve releases repository │
└──────────────────────────────┘       │ • Validate repo configuration │
        │                              │ • Check duplicate release      │
        │                              └──────────────────────────────┘
        │                                             │
        └──────────────────────┬──────────────────────┘
                               │
                               ▼
                    ┌──────────────────────────┐
                    │   Pre-Build Validation   │
                    │      Jobs 1–5 Passed     │
                    └──────────────────────────┘
                               │
                  ┌────────────┴────────────┐
                  │                         │
                  ▼                         ▼
       ┌──────────────────────┐  ┌──────────────────────┐
       │ 6. Unit Tests        │  │ 7. Android Lint      │
       ├──────────────────────┤  ├──────────────────────┤
       │ • Checkout           │  │ • Checkout           │
       │ • Setup Java 17      │  │ • Setup Java 17      │
       │ • Setup Gradle       │  │ • Setup Gradle       │
       │ • Run unit tests     │  │ • Setup signing      │
       │ • Upload report      │  │ • Run lintRelease    │
       │   on failure         │  │ • Upload report      │
       └──────────────────────┘  │   on failure         │
                  │               │ • Cleanup signing   │
                  │               └──────────────────────┘
                  │                         │
                  └────────────┬────────────┘
                               │
                               ▼
                    ┌──────────────────────────┐
                    │ 8. Signed APK Build      │
                    ├──────────────────────────┤
                    │ • Checkout source        │
                    │ • Setup Java 17           │
                    │ • Setup Gradle             │
                    │ • Setup signing            │
                    │ • assembleRelease          │
                    │ • Validate APK exists      │
                    │ • Calculate SHA-256        │
                    │ • Upload APK artifact      │
                    │ • Archive R8 mapping       │
                    │ • Cleanup signing material│
                    └──────────────────────────┘
                               │
                               ▼
                    ┌──────────────────────────┐
                    │ 9. APK Inspection         │
                    ├──────────────────────────┤
                    │ • Download APK            │
                    │ • Verify SHA-256           │
                    │ • Verify APK signature     │
                    │ • Verify certificate       │
                    │   fingerprint              │
                    │ • Verify versionName       │
                    │ • Verify versionCode       │
                    │ • Verify package name      │
                    │ • Verify not debuggable    │
                    └──────────────────────────┘
                               │
                               ▼
                    ┌──────────────────────────┐
                    │ 10. Publish Release       │
                    ├──────────────────────────┤
                    │ • Checkout release notes  │
                    │ • Download inspected APK  │
                    │ • Rename APK               │
                    │ • Generate SHA-256 file    │
                    │ • Verify local hash        │
                    │ • Compose release body    │
                    │ • Add SHA-256 to notes     │
                    │ • Create GitHub release    │
                    │ • Download published APK  │
                    │ • Verify published hash   │
                    │ • Write run summary       │
                    └──────────────────────────┘
                               │
                               ▼
                         🚀 Published APK
```
