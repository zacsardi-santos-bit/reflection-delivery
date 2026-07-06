Implement privilege enforcement for database catalog views to ensure users only see metadata for objects they have access to. Update the system to handle built-in schemas and rename a constant for clarity.

*   Update the Schemas class:
    *   Replace the existing constant `READ_ONLY_SCHEMAS` with `READ_ONLY_SYSTEM_SCHEMAS`.
    *   Ensure all references to `READ_ONLY_SCHEMAS` are updated to `READ_ONLY_SYSTEM_SCHEMAS`.

*   Modify privilege checking logic:
    *   Ensure `ensureUserHasPrivilege` does not throw exceptions for:
        *   SCHEMA class with identifier 'pg_catalog'.
        *   TABLE class with any table in 'pg_catalog' (e.g., 'pg_catalog.pg_am', 'pg_catalog.pg_database').

*   Restrict catalog view access based on user privileges:
    *   For `pg_catalog.pg_class`, return entries only for tables the user has DQL access to.
    *   For `pg_catalog.pg_proc`, return entries only for functions in schemas the user has DQL access to.
    *   For `pg_catalog.pg_namespace`, always show built-in schemas (information_schema, pg_catalog, sys) to all users. User-created schemas should only be visible after DQL is granted.
    *   For `pg_catalog.pg_attribute`, return column entries only for tables the user has DQL access to.
    *   For `pg_catalog.pg_constraint`, return constraint entries only for tables the user has DQL access to.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.