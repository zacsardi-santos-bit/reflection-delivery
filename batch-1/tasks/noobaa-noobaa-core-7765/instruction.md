Implement enhancements to the NooBaa NC NSFS health check system to properly validate accounts identified by OS usernames. Ensure that the system can resolve usernames to user IDs and check directory access permissions accurately.

*   Update `get_fs_context` in `src/util/native_fs_utils.js`:
    *   Accept an `account_data` object with either a `distinguished_name` (OS username) or `uid` and `gid`.
    *   Resolve the `distinguished_name` to obtain the corresponding `uid` and `gid` if provided.
    *   Return a filesystem context object suitable for permission checks.

*   Update `is_dir_rw_accessible` in `src/util/native_fs_utils.js`:
    *   Accept a `fs_context` and a `path`.
    *   Return truthy if the directory at the given path is both readable and writable for the identity in `fs_context`.
    *   Correctly evaluate Unix permission bits (owner, group, other) based on the `uid/gid` in `fs_context`.

*   Enhance `nc_nsfs_health` check:
    *   Identify accounts with `nsfs_account_config` using a `distinguished_name` that refers to a valid OS user but lacks read-write access to `new_buckets_path`.
        *   Report these accounts in `invalid_accounts` with code `ACCESS_DENIED`.
    *   Identify accounts with `nsfs_account_config` using a `distinguished_name` that does not correspond to any existing OS user.
        *   Report these accounts in `invalid_accounts` with code `INVALID_DISTINGUISHED_NAME`.

*   Implement cross-platform utility functions in `src/test/system_tests/test_utils.js`:
    *   `create_fs_user_by_platform(new_user, new_password, uid, gid) -> Promise<void>`:
        *   Create an OS user with the specified username, password, `uid`, and `gid`.
        *   On macOS, use `dscl` commands; on Linux, use `groupadd` and `useradd`.
        *   Export this function.
    *   `delete_fs_user_by_platform(name) -> Promise<void>`:
        *   Remove the OS user with the given name and clean up their home directory.
        *   On macOS, use `dscl delete` and `rm -rf`; on Linux, use `userdel -r`.
        *   Export this function.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.