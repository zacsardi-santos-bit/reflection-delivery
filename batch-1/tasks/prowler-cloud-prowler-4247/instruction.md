Implement four new security checks for DocumentDB clusters in your cloud security scanning tool. These checks should evaluate backup retention, log export configuration, deletion protection, and storage encryption at the cluster level. Update the service layer to expose necessary cluster data, and remove the existing instance-level encryption check.

*   Define the `DBCluster` model in `prowler/providers/aws/services/documentdb/documentdb_service.py` as a Pydantic `BaseModel` with the following fields:
    *   `id: str`
    *   `arn: str`
    *   `endpoint: Optional[str]`
    *   `engine: str`
    *   `status: str`
    *   `encrypted: bool`
    *   `backup_retention_period: int = 0`
    *   `cloudwatch_logs: Optional[list] = []`
    *   `deletion_protection: bool`
    *   `multi_az: bool`
    *   `parameter_group: str`
    *   `region: str`
    *   `tags: Optional[list] = []`

*   Update the DocumentDB service class:
    *   Add a `db_clusters` attribute (dict keyed by cluster ARN) populated by calling the `DescribeDBClusters` AWS API filtered by engine 'docdb'.
    *   Map API response fields to `DBCluster` fields: `DBClusterIdentifier` to `id`, `DBClusterArn` to `arn`, `Engine` to `engine`, `Status` to `status`, `StorageEncrypted` to `encrypted`, `BackupRetentionPeriod` to `backup_retention_period`, `EnabledCloudwatchLogsExports` to `cloudwatch_logs`, `DeletionProtection` to `deletion_protection`, `DBClusterParameterGroup` to `parameter_group`, `MultiAZ` to `multi_az`.

*   Implement the `documentdb_cluster_backup_enabled` check:
    *   Iterate over `db_clusters`.
    *   If `backup_retention_period` is 0, return FAIL with `status_extended` "DocumentDB Cluster {id} does not have backup enabled."
    *   If `backup_retention_period` is greater than 0 but not greater than the configured minimum (default 7), return FAIL with `status_extended` "DocumentDB Cluster {id} has backup enabled with retention period {N} days. Recommended to increase the backup retention period to a minimum of 7 days."
    *   If `backup_retention_period` is strictly greater than the minimum, return PASS with `status_extended` "DocumentDB Cluster {id} has backup enabled with retention period {N} days."

*   Implement the `documentdb_cluster_cloudwatch_log_export` check:
    *   Iterate over `db_clusters`.
    *   If `cloudwatch_logs` is empty, return FAIL with `status_extended` "DocumentDB Cluster {id} does not have cloudwatch log export enabled."
    *   If `cloudwatch_logs` contains only 'audit' or 'profiler', return FAIL with `status_extended` "DocumentDB Cluster {id} is only shipping {log_type} to CloudWatch Logs. Recommended to ship both Audit and Profiler logs."
    *   If `cloudwatch_logs` contains both 'audit' and 'profiler', return PASS with `status_extended` "DocumentDB Cluster {id} is shipping {joined_logs} to CloudWatch Logs."

*   Implement the `documentdb_cluster_deletion_protection` check:
    *   Iterate over `db_clusters`.
    *   If `deletion_protection` is False, return FAIL with `status_extended` "DocumentDB Cluster {id} does not have deletion protection enabled."
    *   If `deletion_protection` is True, return PASS with `status_extended` "DocumentDB Cluster {id} has deletion protection enabled."

*   Implement the `documentdb_cluster_storage_encrypted` check:
    *   Iterate over `db_clusters`.
    *   If `encrypted` is False, return FAIL with `status_extended` "DocumentDB Cluster {id} is not encrypted at rest."
    *   If `encrypted` is True, return PASS with `status_extended` "DocumentDB Cluster {id} is encrypted at rest."

*   Ensure each check sets `report.region`, `report.resource_id` (to cluster id), and `report.resource_arn` (to cluster arn) on each result.
*   Remove the existing instance-level storage encryption check (`documentdb_instance_storage_encrypted`).

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.