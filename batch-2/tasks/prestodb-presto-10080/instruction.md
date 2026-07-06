Implement a configurable security system for the Raptor connector in Presto, allowing administrators to enforce access control policies. Support three security modes: "allow all", "read-only", and "file-based" using a JSON configuration file.

*   Update the `RaptorSecurityConfig` class in `com.facebook.presto.raptor.security`:
    *   Implement `setSecuritySystem(String)` annotated with `@Config("raptor.security")` to return the config instance for builder chaining.
    *   Default the security system to "none".
    *   Accept only "none", "file", and "read-only" as valid security system names.

*   Modify the `RaptorConnector` class:
    *   Update the constructor to accept a `ConnectorAccessControl` parameter.
    *   Override `getAccessControl()` to expose the `ConnectorAccessControl`.

*   Enhance `RaptorQueryRunner`:
    *   Add a new 4-parameter `createRaptorQueryRunner` method accepting `Map<String, String> extraRaptorProperties`.
    *   Ensure the existing 3-parameter method delegates to the new overload with an empty map for `extraRaptorProperties`.

*   Implement security behavior:
    *   For `raptor.security=read-only`, block table creation with a `RuntimeException` matching ".*Access Denied: Cannot create .*".
    *   For `raptor.security=file`, use a JSON rules file specified by `security.config-file` to manage user privileges.
        *   Allow users with SELECT privilege in the "tables" array to execute SELECT queries.
        *   Deny access to users without SELECT privilege, throwing a `RuntimeException` matching ".*Access Denied: Cannot select from table tpch.orders.*".

*   Define the JSON rules file format:
    *   Include a top-level "tables" array with objects containing "user" and "privileges" fields.
    *   Include a top-level "schemas" array with objects containing "user" and "owner" fields.

*   Install `RaptorSecurityModule` in the connector factory:
    *   Conditionally bind `AllowAllAccessControl`, `ReadOnlySecurityModule`, or `FileBasedAccessControlModule` based on `raptor.security`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.