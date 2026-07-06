## Description

Several issues need to be addressed around the online upgrade mechanism for the non-containerized storage system.

First, the error message thrown when a downgrade is attempted is misleading. It currently references "container versions" which doesn't clearly communicate the problem — that the version being installed is actually older than what the server is already running. The message should be updated to clearly describe the situation: that you're attempting to install an older version while the server is already at a newer one.

Second, integration test coverage is needed to verify that the online upgrade blocking mechanism behaves correctly for CLI and S3 operations. During a rolling upgrade, hosts with an outdated configuration directory version should block configuration-modifying operations (creating, updating, or deleting buckets and accounts) to prevent corruption, while read-only operations (listing and status queries) should continue to work normally. The same principle applies to S3 API calls: bucket modification operations should fail with an internal error when the configuration directory is outdated, while object operations and read-only bucket operations should remain unaffected.

Third, the lifecycle rule status validation should return a more appropriate error. When a lifecycle rule's status value is not one of the two valid options (the correctly-capitalized enabled and disabled states), the API should return a malformed request error rather than an invalid argument error.

Finally, bucket policy evaluation needs to be fixed so that explicit DENY statements for a principal take precedence over ALLOW statements matching all principals. Currently, when a policy has one statement denying a specific account and another allowing all principals, the deny may not be respected correctly.

## Expected Behavior

- Attempting a system downgrade produces a clear error message indicating the new version is older than the current server version.
- CLI bucket and account create/update/delete operations fail with a "config directory update blocked" error when the host's configuration directory version is outdated.
- CLI bucket and account list/status operations succeed regardless of the configuration directory version.
- S3 create/delete/update bucket operations fail with an internal error when the configuration directory version is outdated.
- S3 object operations (read, write, delete) are unaffected by the configuration directory version.
- S3 head bucket operations are unaffected by the configuration directory version.
- Lifecycle rule configurations with invalid status values (not the exact valid strings) are rejected with a malformed XML error.
- DENY bucket policy statements for a specific principal take precedence over ALLOW statements for all principals.

## Why This Matters

These changes are critical for safe online rolling upgrades in multi-host deployments. Without proper blocking of write operations during an upgrade window, hosts running old code could corrupt the shared configuration directory. Clear error messages and comprehensive integration tests reduce operational risk and improve operator confidence when performing upgrades.
