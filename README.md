## ledgerbook-releases

## Release Workflow Structure
```text
Production Release Pipeline (10 Groups · 25 Steps)
│
├── 01. Source & Preflight
│   ├── 01/25 - Checkout source code
│   └── 02/25 - Preflight -- required secrets 
│
├── 02. Release Validation
│   ├── 03/25 - Validate release version
│   ├── 04/25 - Validate release notes
│   ├── 05/25 - Resolve releases repository
│   └── 06/25 - Preflight -- release not already published
│
├── 03. Build Environment
│   ├── 07/25 - Setup Java 17
│   ├── 08/25 - Setup Gradle
│   └── 09/25 - Prepare Gradle wrapper
│
├── 04. Signing Setup
│   ├── 10/25 - Restore signing keystore
│   └── 11/25 - Write keystore.properties
│
├── 05. Quality Checks
│   ├── 12/25 - Run Android Lint
│   ├── 13/25 - Run test suite
│   └── 14/25 - Upload test & lint reports
│
├── 06. Build Release
│   └── 15/25 - Build signed release APK
│
├── 07. Verify Release APK
│   ├── 16/25 - Verify APK exists
│   ├── 17/25 - Verify signature & certificate fingerprint
│   └── 18/25 - Verify APK metadata matches tag
│
├── 08. Release Artifacts
│   ├── 19/25 - Archive R8 mapping & debug symbols
│   ├── 20/25 - Package release artifact
│   ├── 21/25 - Back up signed APK to CI run
│   └── 22/25 - Generate release summary
│
├── 09. Publish Release
│   ├── 23/25 - Publish GitHub release
│   └── 24/25 - Verify published asset
│
└── 10. Cleanup
    └── 25/25 - Cleanup signing material
