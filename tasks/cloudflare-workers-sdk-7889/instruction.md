Implement improvements to the automatic resource provisioning feature for Cloudflare Workers deployments. Simplify the output, eliminate unnecessary confirmation dialogs, ensure D1 databases are reused when possible, and address jurisdiction mismatches for R2 buckets.

*   Simplify provisioning success messages:
    *   Output message format: ✨ <BINDING_NAME> provisioned 🎉 (use binding name from config).
    *   Remove separator lines from the provisioning output.

*   Eliminate confirmation dialogs:
    *   When a D1 database or R2 bucket name is specified in the configuration and no pre-existing resource is found, proceed to create the resource without confirmation dialogs.

*   Implement D1 database name lookup:
    *   Use the D1 API endpoint GET /accounts/:accountId/d1/database/:name to check for existing databases.
    *   If a database object with a UUID is returned, use the existing database's UUID for the worker binding.
    *   If error code 7404 (database not found) is returned, create the database without confirmation.

*   Address R2 jurisdiction inheritance:
    *   Ensure the jurisdiction of an existing R2 bucket binding matches the jurisdiction specified in the configuration.
    *   If jurisdictions differ, do not inherit the binding; provision a new bucket with the correct jurisdiction.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.