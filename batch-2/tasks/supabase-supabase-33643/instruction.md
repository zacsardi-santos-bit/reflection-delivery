Implement column-level privilege management in the pg-meta library to allow listing, granting, and revoking of privileges for specific columns in a PostgreSQL database. Ensure the solution handles table and column names with spaces or special characters.

*   Update the default export in `packages/pg-meta/src/index.ts` to include a `columnPrivileges` property with `list`, `grant`, and `revoke` functions.
*   Implement the `columnPrivileges.list()` function in `packages/pg-meta/src/pg-meta-column-privileges.ts`:
    *   Return an object with a `sql` string and a `zod` schema for parsing results into an array of column privilege records.
    *   Each record must include `column_id`, `relation_schema`, `relation_name`, `column_name`, and `privileges`.
    *   The `privileges` array must contain objects with `grantor`, `grantee`, `privilege_type`, and `is_grantable`.
    *   Accept an optional `columnIds` parameter to filter results by specific columns.
*   Implement the `columnPrivileges.grant()` function:
    *   Accept an array of grant descriptor objects with `columnId`, `grantee`, `privilegeType`, and optional `isGrantable`.
    *   Return an object with a `sql` string that grants specified privileges.
    *   Ensure granting 'ALL' results in INSERT, SELECT, UPDATE, and REFERENCES privileges.
    *   Handle names with spaces or special characters requiring SQL quoting.
*   Implement the `columnPrivileges.revoke()` function:
    *   Accept an array of revoke descriptor objects with `columnId`, `grantee`, and `privilegeType`.
    *   Return an object with a `sql` string that revokes specified privileges.
    *   Handle names with spaces or special characters requiring SQL quoting.
*   Ensure that after executing the SQL from `grant()` or `revoke()`, subsequent `list()` calls reflect the changes in privileges.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.