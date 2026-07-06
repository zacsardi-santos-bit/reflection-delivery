Implement the necessary changes to improve the online upgrade mechanism for the non-containerized storage system. Update error messages, ensure correct operation blocking during upgrades, and enhance lifecycle rule validation and bucket policy evaluation.

*   Update the error message for downgrade attempts:
    *   Modify the `should_upgrade` function in `src/upgrade/upgrade_utils.js` to throw an error with the message 'attempt to upgrade to an older version while server\'s version is newer' when the `new_version` is older than the `current_version`.

*   Implement test utilities for system configuration:
    *   Export `create_system_json` from `src/test/system_tests/test_utils.js` with the signature `create_system_json(config_fs: ConfigFS, mock_config_dir_version?: string) -> Promise<void>`.
        *   Use `config_fs._get_new_system_json_data()` to obtain default system data.
        *   Optionally override `system_data.config_directory.config_dir_version` with `mock_config_dir_version`.
        *   Write the JSON data using `config_fs.create_system_config_file(JSON.stringify(system_data))`.
    *   Export `update_system_json` from `src/test/system_tests/test_utils.js` with the signature `update_system_json(config_fs: ConfigFS, mock_config_dir_version?: string) -> Promise<void>`.
        *   Read existing system JSON using `config_fs.get_system_config_file()`.
        *   Optionally override `system_data.config_directory.config_dir_version` with `mock_config_dir_version`.
        *   Write back the updated JSON using `config_fs.update_system_config_file(JSON.stringify(system_data))`.

*   Ensure correct CLI operation behavior based on configuration version:
    *   CLI bucket write operations (add, update, delete) must return `ManageCLIError.ConfigDirUpdateBlocked.message` when `system.json` has an outdated `config_dir_version`.
    *   CLI bucket read operations (list, status) must succeed regardless of `config_dir_version`.
    *   CLI account write operations (add, update, delete) must return `ManageCLIError.ConfigDirUpdateBlocked.message` when `system.json` has an outdated `config_dir_version`.
    *   CLI account read operations (list, status) must succeed regardless of `config_dir_version`.

*   Adjust S3 operation behavior based on configuration version:
    *   The `putBucketLifecycleConfiguration` operation must return a MalformedXML error if a lifecycle rule's Status is not exactly 'Enabled' or 'Disabled'.
    *   S3 bucket write operations (createBucket, deleteBucket, putBucketPolicy) must return an InternalError when `system.json` has an outdated `config_dir_version`.
    *   S3 object operations (putObject, getObject, headObject, deleteObject) must succeed regardless of `config_dir_version`.
    *   S3 read-only bucket operations (headBucket) must succeed regardless of `config_dir_version`.

*   Correct bucket policy evaluation:
    *   Ensure DENY statements for a specific principal take precedence over ALLOW statements for all principals.
    *   Ensure DENY for all principals on a specific action takes precedence over ALLOW for a specific account, regardless of statement order.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.