Implement the ability to remove specific attributes from a group and correct the validation logic for numeric identifiers in the kanidm identity management system. Ensure that administrators can purge group attributes and correctly assign numeric identifiers within the valid range.

*   Add a method `idm_group_purge_attr` to `KanidmClient` in `libs/client/src/group.rs`:
    *   Accepts a group ID string and an attribute name string.
    *   Sends a DELETE request to the endpoint `/v1/group/{id}/_attr/{attr}`.
    *   Returns `Result<(), ClientError>`.
    *   Must return `Ok(())` on success.

*   Export `ATTR_GIDNUMBER` as a constant from `kanidm_proto::constants`:
    *   Ensure it can be imported alongside constants like `KSESSIONID`.

*   Update validation logic for numeric identifiers:
    *   Allow manually specified GID of 59999 when extending a group with POSIX/Unix attributes using `idm_group_unix_extend`, even if the group already has an existing GID.
    *   Ensure that purging a group's GID attribute via `idm_group_purge_attr` and setting a new GID with `idm_group_unix_extend` using a value like 123123 succeeds and returns `Ok(())`.
    *   Accept a manually specified UID of 5000 when extending a service account with POSIX/Unix attributes using `idm_service_account_unix_extend`, returning `Ok(())`.
    *   Reject a manually specified UID of 999 when extending a service account with POSIX/Unix attributes using `idm_service_account_unix_extend`, returning `Err(...)`. Values below 1000 must be considered invalid as they are system-reserved.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.