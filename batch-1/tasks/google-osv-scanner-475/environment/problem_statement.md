## Description

When running vulnerability scanning in a CI pipeline, comparing a new scan against an old one can produce false positives if project files were simply reorganized. Specifically, if a lockfile is moved from one directory to another (e.g., during a refactoring), the existing comparison logic treats the same vulnerabilities as newly introduced because the source file path changed. This means pull requests that do nothing more than reorganize the project layout will incorrectly trigger vulnerability alerts.

## Expected Behavior

- A new comparison function should be available that counts vulnerability occurrences across the entire scan by their unique identifier, ignoring which source file they came from.
- When the total count of a given vulnerability ID has not increased between the old and new scans, that vulnerability should not be reported as new — even if the files containing it were moved.
- When a vulnerability ID genuinely appears more times in the new scan than in the old one (i.e., a real new dependency was added), it should be included in the result with the excess occurrence count.
- When the old scan has more occurrences of a vulnerability than the new scan, the result should be empty (the vulnerability was removed, not added).
- The existing comparison logic (which compares by source file path) should remain and be used when the occurrence-based comparison confirms there are genuinely new vulnerabilities.

## Why This Matters

Developers performing routine project reorganization (renaming directories, moving lockfiles) should not see false vulnerability alerts in CI. This change makes the CI pipeline smarter about distinguishing structural code changes from changes that genuinely introduce new security risks.
