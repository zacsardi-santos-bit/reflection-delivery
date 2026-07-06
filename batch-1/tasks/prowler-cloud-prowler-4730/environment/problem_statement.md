## Description

The existing RDS security checks in Prowler (`rds_instance_default_admin` and `rds_instance_iam_authentication_enabled`) are currently combining both RDS instances and RDS clusters into a single check. This creates confusion and makes it difficult to distinguish findings for standalone RDS instances versus clustered RDS deployments.

The checks need to be separated so that:
1. Instance-specific checks only evaluate non-clustered RDS instances
2. Cluster-specific checks are created to separately evaluate RDS clusters

## Expected Behavior

- A new `rds_cluster_default_admin` check should validate that RDS clusters are not using default master usernames ("admin" or "postgres")
- A new `rds_cluster_iam_authentication_enabled` check should validate that RDS clusters have IAM authentication enabled
- The existing `rds_instance_default_admin` check should only evaluate non-clustered RDS instances and include "which is not clustered" in its status messages
- The existing `rds_instance_iam_authentication_enabled` check should only evaluate non-clustered RDS instances and include "which is not clustered" in its status messages
- Both new cluster checks should support Aurora PostgreSQL, Aurora MySQL, and other relevant database engines

## Current Behavior

Currently:
- `rds_instance_default_admin` iterates over both `db_instances` and `db_clusters`, producing mixed findings
- `rds_instance_iam_authentication_enabled` iterates over both `db_instances` and `db_clusters`, producing mixed findings
- Status messages don't clearly indicate whether the finding applies to an instance or cluster
- There are no dedicated cluster-specific checks
