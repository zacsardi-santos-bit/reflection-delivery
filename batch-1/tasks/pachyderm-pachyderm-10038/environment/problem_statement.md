## Description

Pipeline version numbers stored in the database can sometimes become inconsistent — multiple pipeline records end up sharing the same version number due to a bug in how versions are assigned. This corrupted state means the system cannot enforce uniqueness on pipeline versions, which causes failures when listing pipelines, listing jobs associated with a pipeline, or creating new pipeline versions.

A database migration is needed to detect and repair this corrupt state: it should find all pipelines with duplicate version numbers, reassign version numbers sequentially, update all related job records to reference the corrected versions, and then enforce a uniqueness constraint so the problem cannot silently persist.

## Expected Behavior

- The migration should detect duplicate pipeline version numbers across all pipelines.
- For each pipeline with duplicates, versions should be renumbered sequentially starting from 1 based on creation order.
- Job records that reference the old pipeline versions should be updated to use the corrected version numbers.
- After the migration, a unique constraint on pipeline versions should be created and enforced in the database.
- After the migration, listing pipelines, listing jobs, and creating new pipeline versions should all succeed without error.
- The migration should be structured so that the pre-deduplication state can be reached independently (e.g., for testing purposes), and the deduplication step applied separately.

## Related Change

A migration state that was previously only accessible within its own package needs to be made accessible to other packages so they can reference it as a starting point for building on or testing against the pre-deduplication cluster state.

## Why This Matters

Without this fix, clusters that have encountered the duplicate-version bug are in a broken state where pipeline and job listing fails. The migration provides a path to repair the data and prevent the issue from recurring.
