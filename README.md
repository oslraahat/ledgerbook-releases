## `LedgerBook Release Workflow Structure`
# Android Release Workflow

## Release Flow

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

---

## Job 1 — Preflight & Validate

### Steps

1. **Checkout source code**

   * Checkout the exact commit associated with the release tag.

2. **Validate required secrets**

   * Verify all required GitHub Actions secrets are available.
   * Fail early if any required secret is missing.

3. **Validate release version**

   * Validate release tag format (`v*`).
   * Verify `versionName` matches the Git tag.
   * Verify `versionCode` is monotonic.

4. **Validate release notes**

   * Verify the release notes file exists for the current tag.
   * Example:
     `docs/release-notes/v1.2.3.md`

5. **Resolve releases repository**

   * Validate that the configured public releases repository is accessible.

6. **Duplicate release check**

   * Verify that the current release version has not already been published.
   * Prevent accidental duplicate releases.

---

## Job 2 — Quality Checks

### Steps

7. **Setup Java 17**

   * Configure the required JDK version for the Android build.

8. **Validate Gradle wrapper**

   * Verify the Gradle wrapper files are present and valid.

9. **Setup Gradle**

   * Configure Gradle and its dependency/cache environment.

10. **Prepare Gradle wrapper**

    * Ensure the wrapper is executable and ready for the build.

11. **Run Android Lint**

    * Execute Android Lint checks.
    * Fail the workflow when release-blocking issues are detected.

12. **Run unit tests**

    * Execute the release validation test suite.

13. **Upload test/lint reports**

    * Upload test and lint reports for troubleshooting.
    * Reports should remain available when a quality check fails.

---

## Job 3 — Build & Verify

### Steps

14. **Restore signing keystore**

    * Restore the release signing keystore from the configured secret.
    * Generate `keystore.properties`.

15. **Build signed release APK**

    * Build the production release APK using the release signing configuration.

16. **Verify APK exists**

    * Confirm the expected APK was successfully generated.

17. **Verify APK signature**

    * Verify that the APK is properly signed.
    * Reject unsigned or incorrectly signed APKs.

18. **Verify signing certificate**

    * Extract the signing certificate.
    * Compare its SHA-256 fingerprint with the expected fingerprint.

19. **Verify APK version metadata**

    * Verify:

      * `versionName`
      * `versionCode`
      * `debuggable = false`

20. **Calculate APK SHA-256**

    * Calculate the SHA-256 hash of the verified APK.
    * Preserve the hash for later package and publication verification.

21. **Upload verified APK**

    * Upload the verified APK as a workflow artifact.

22. **Upload R8 mapping/symbols**

    * Preserve R8 mapping and debug symbol files for release diagnostics.

23. **Shred signing material**

    * Securely remove temporary signing files and sensitive keystore material after the build.

---

## Job 4 — Package

### Steps

24. **Archive R8 mapping & symbols**

    * Package R8 mapping and debug symbols into the release artifact.

25. **Package release artifact**

    * Rename the APK using the release version.
    * Generate the `.sha256` checksum file.
    * Verify the generated checksum.
    * Compare the package hash with the hash generated during the build.

26. **Upload release artifact**

    * Upload the final release package for the publish job.

---

## Job 5 — Publish

### Steps

27. **Publish release**

    * Publish the verified release to the public releases repository.
    * Include:

      * APK
      * SHA-256 checksum
      * Release notes
      * R8 mapping/symbols where applicable

28. **Download published APK**

    * Download the APK from the public release location.

29. **Verify published APK exists**

    * Confirm the published APK is available and accessible.

30. **Verify published APK SHA-256**

    * Calculate the SHA-256 hash of the downloaded APK.
    * Compare it with the original build hash.
    * Fail if the hashes do not match.

31. **Write GitHub release summary**

    * Write the final release status to the GitHub Actions summary.
    * Include:

      * Release version
      * APK filename
      * SHA-256
      * Publication status
      * Verification status

---

## Release Validation Chain

```text
Tag
 │
 ├── Version Validation
 │
 ├── Release Notes Validation
 │
 ├── Duplicate Check
 │
 ├── Lint
 │
 ├── Unit Tests
 │
 ├── Signed APK Build
 │
 ├── APK Signature Verification
 │
 ├── Certificate SHA-256 Verification
 │
 ├── Version Metadata Verification
 │
 ├── APK SHA-256
 │
 ├── Package
 │
 ├── Publish
 │
 └── Published APK SHA-256 Verification
       │
       ▼
   Release Confirmed
```

## Failure Principle

প্রতিটি Job-এর output পরবর্তী Job-এর input হিসেবে কাজ করবে। কোনো critical validation ব্যর্থ হলে workflow পরবর্তী Job-এ এগোবে না।

```text
Preflight Failed
      ↓
STOP

Quality Check Failed
      ↓
STOP

Build / Signature Verification Failed
      ↓
STOP

Package Verification Failed
      ↓
STOP

Publish / Published APK Verification Failed
      ↓
RELEASE NOT CONFIRMED
```
