Implement security checks for Azure MySQL Flexible Server instances to ensure proper configurations for audit logging, connection encryption, and encryption protocol versions. Develop the underlying service layer to retrieve server instances and configurations from Azure, and create checks that return pass or fail results based on server settings.

*   Implement the MySQL service module:
    *   Define a `MySQL` class, a `FlexibleServer` dataclass, and a `Configuration` dataclass in `prowler/providers/azure/services/mysql/mysql_service.py`.
    *   Ensure `FlexibleServer` includes fields: `resource_id` (str), `location` (str), `version` (str), and `configurations` (dict mapping configuration name to `Configuration` objects).
    *   Ensure `Configuration` includes fields: `resource_id` (str), `description` (str), and `value` (str).
    *   The `MySQL` class should have:
        *   A `flexible_servers` attribute mapping subscription names to dicts of server names to `FlexibleServer` objects.
        *   A `clients` attribute mapping subscription names to `MySQLManagementClient` instances.
        *   Implement `__get_configurations__()` to return a dict mapping configuration names to `Configuration` objects.

*   Develop security checks:
    *   `mysql_flexible_server_audit_log_connection_activated`:
        *   Return PASS if 'CONNECTION' is in `audit_log_events` configuration value; FAIL otherwise.
        *   Use resource_id of the `audit_log_events` configuration in results.
        *   Status messages: "Audit log is enabled for server {server_name} in subscription {subscription_name}." for PASS, "Audit log is disabled for server {server_name} in subscription {subscription_name}." for FAIL.
    *   `mysql_flexible_server_audit_log_enabled`:
        *   Return PASS if `audit_log_enabled` value is 'ON'; FAIL if 'OFF'.
        *   Use resource_id of the `audit_log_enabled` configuration in results.
        *   Status messages: "Audit log is enabled for server {server_name} in subscription {subscription_name}." for PASS, "Audit log is disabled for server {server_name} in subscription {subscription_name}." for FAIL.
    *   `mysql_flexible_server_minimum_tls_version_12`:
        *   Return PASS if all TLS versions are TLSv1.2 or TLSv1.3; FAIL if any version is older.
        *   If `tls_version` is absent, use server name as resource_id and return FAIL.
        *   Status messages: "TLS version is {value} in server {server_name} in subscription {subscription_name}. This version of TLS is considered secure." for PASS, "TLS version is {value} in server {server_name} in subscription {subscription_name}. There is at leat one version of TLS that is considered insecure." for FAIL.
    *   `mysql_flexible_server_ssl_connection_enabled`:
        *   Return PASS if `require_secure_transport` is 'ON'; FAIL if 'OFF'.
        *   If `require_secure_transport` is absent, use server name as resource_id and return FAIL.
        *   Status messages: "SSL connection is enabled for server {server_name} in subscription {subscription_name}." for PASS, "SSL connection is disabled for server {server_name} in subscription {subscription_name}." for FAIL.

*   Ensure all checks:
    *   Return an empty list when no subscriptions or servers are found.
    *   Support multiple servers per subscription, producing one result per server per subscription in a single `execute()` call.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.