## Description

Cloud-nuke currently supports discovering and deleting S3 buckets, but it does not handle any of the S3 access point variants. Users who create standard S3 access points, object lambda access points, or multi-region access points in their AWS accounts cannot clean them up using cloud-nuke. This gap means that cleanup runs leave these resources behind, accumulating costs and clutter.

## Expected Behavior

- Cloud-nuke should be able to list and delete **S3 access points** (standard), respecting name-based exclusion filters.
- Cloud-nuke should be able to list and delete **S3 Object Lambda access points**, respecting name-based exclusion filters.
- Cloud-nuke should be able to list and delete **S3 Multi-Region access points**, respecting both name-based and time-based exclusion filters (so users can skip access points created before a given date).
- The configuration system should support per-type filtering rules for each of these three new resource types, consistent with how other resources are configured.

## Why This Matters

Teams running automated cleanup pipelines rely on cloud-nuke to fully wipe test environments. Without support for these access point types, leftover resources can cause name conflicts, unexpected costs, or failed re-deployments in fresh environments.
