## Description

The security scanning tool currently evaluates DocumentDB at the individual instance level for a single check (storage encryption). However, DocumentDB clusters are the primary management unit, and several important security and compliance properties are configured at the cluster level, not the instance level. There is no coverage for cluster backup retention, log export configuration, or deletion protection, and the existing instance-level encryption check does not align with how AWS Security Hub reports on DocumentDB compliance.

## Expected Behavior

- A check should evaluate whether DocumentDB clusters have automated backups enabled with a sufficient retention period (configurable minimum, defaulting to 7 days). Clusters with no backup configured should fail; clusters with some backup but below the minimum should also fail with guidance to increase the period; clusters meeting the minimum should pass.
- A check should evaluate whether DocumentDB clusters are exporting both audit and profiler logs to the centralized monitoring service. Clusters with no logs, or only one log type enabled, should fail; clusters exporting both should pass.
- A check should evaluate whether DocumentDB clusters have deletion protection enabled. Clusters without it should fail; clusters with it should pass.
- A check should evaluate whether DocumentDB cluster storage is encrypted at rest. Unencrypted clusters should fail; encrypted clusters should pass.
- The old instance-level storage encryption check should be removed in favor of the new cluster-level check.
- The underlying service layer must expose cluster data so all four checks can operate on it.

## Why This Matters

Security teams need cluster-level visibility to satisfy common compliance frameworks and AWS Security Hub controls for DocumentDB. Without these checks, critical misconfigurations like missing backups, absent deletion protection, disabled audit logs, or unencrypted storage go undetected at the cluster level.
