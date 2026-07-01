Refactor the RDS security checks in Prowler to separate instance and cluster checks. Implement new checks for RDS clusters and modify existing checks to ensure clarity between standalone instances and clustered deployments.

*   Implement a new class `rds_cluster_default_admin` in `prowler/providers/aws/services/rds/rds_cluster_default_admin/rds_cluster_default_admin.py`.
    *   Ensure it inherits from the `Check` base class.
    *   Implement the `execute` method to:
        *   Return 'FAIL' with the message 'RDS Cluster {cluster_id} is using the default master username.' if the cluster uses 'admin' or 'postgres'.
        *   Return 'PASS' with the message 'RDS Cluster {cluster_id} is not using the default master username.' otherwise.
        *   Return an empty list if no RDS clusters exist.

*   Implement a new class `rds_cluster_iam_authentication_enabled` in `prowler/providers/aws/services/rds/rds_cluster_iam_authentication_enabled/rds_cluster_iam_authentication_enabled.py`.
    *   Ensure it inherits from the `Check` base class.
    *   Implement the `execute` method to:
        *   Return 'FAIL' with the message 'RDS Cluster {cluster_id} does not have IAM authentication enabled.' if IAM auth is disabled.
        *   Return 'PASS' with the message 'RDS Cluster {cluster_id} has IAM authentication enabled.' if IAM auth is enabled.
        *   Return an empty list if no RDS clusters exist.
        *   Apply only to supported engines: 'postgres', 'aurora-postgresql', 'mysql', 'mariadb', 'aurora-mysql', 'aurora'.

*   Modify the existing `rds_instance_default_admin` check.
    *   Ensure it only evaluates non-clustered RDS instances (where `cluster_id` is None).
    *   Update the status message to include 'which is not clustered'.
    *   Remove iteration over `db_clusters`.

*   Modify the existing `rds_instance_iam_authentication_enabled` check.
    *   Ensure it only evaluates non-clustered RDS instances (where `cluster_id` is None).
    *   Update the status message to include 'which is not clustered'.
    *   Remove iteration over `db_clusters`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.