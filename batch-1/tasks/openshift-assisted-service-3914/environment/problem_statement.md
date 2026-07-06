## Description

The cluster database table contains a legacy column that was used to store ignition configuration overrides. This concept has since been superseded — the field no longer appears in the current cluster data model, but the column was never formally removed from the database. This creates schema drift between the Go model and the actual database, and leaves behind a confusing artifact in the database schema.

We need a new database migration to drop this obsolete column cleanly. The migration must be idempotent: it should succeed whether or not the column is present, so it is safe to apply to both existing deployments (which have the column) and fresh installations (which may never have had it).

Additionally, an older migration that previously handled populating infrastructure environment records from cluster data is no longer needed and should be removed along with its tests.

## Expected Behavior

- A new migration is added that removes the obsolete column from the clusters table
- The migration succeeds even when the column does not already exist
- After the migration runs, the column is absent from the clusters table
- The outdated infrastructure environment population migration is removed from the migration chain
- Existing migration tests are updated to use the current install-config overrides field on the cluster model instead of the removed ignition overrides field

## Why This Matters

Keeping unused columns in the database schema causes confusion and technical debt. Without an explicit migration, the column persists indefinitely across deployments. A clean, safe migration ensures all environments converge to the correct schema state without risk of failure on environments that were already cleaned up manually or never had the column.
