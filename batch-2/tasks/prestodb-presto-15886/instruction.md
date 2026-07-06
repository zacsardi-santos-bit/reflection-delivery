Extend the file-based security access control system to support schema-level ownership rules. Implement the ability for administrators to define rules that specify user and schema name patterns, determining schema ownership. Ensure that these rules are evaluated in order, with the first matching rule determining the outcome for schema creation, dropping, and renaming operations.

*   Update the JSON configuration to include a top-level 'schemas' array for schema ownership rules.
    *   Each rule must have an optional 'user' field (Java regex for user names), an optional 'schema' field (Java regex for schema names), and a required 'owner' boolean field.
*   Modify `FileBasedSystemAccessControlRules` to:
    *   Deserialize the 'schemas' JSON array into an `Optional<List<SchemaAccessControlRule>>`.
    *   Expose the deserialized rules via the `getSchemaRules()` method.
*   Implement `SchemaAccessControlRule` to:
    *   Include a `match(String user, String schemaName)` method returning `Optional<Boolean>`.
        *   Return `Optional.of(true)` if both patterns match and owner is true.
        *   Return `Optional.of(false)` if both patterns match and owner is false.
        *   Return `Optional.empty()` if either pattern does not match.
*   Update `FileBasedSystemAccessControl` to:
    *   Use schema ownership rules in `checkCanCreateSchema`, `checkCanDropSchema`, and `checkCanRenameSchema`.
    *   Deny access (throw `AccessDeniedException`) if the user is not a schema owner per the rules.
    *   Ensure `checkCanRenameSchema` verifies ownership of both source and target schema names.
*   Ensure schema ownership evaluation:
    *   Checks catalog-level access first; deny if the user lacks full catalog access.
    *   Allows all schemas if no schema rules are configured and catalog-level access is sufficient.
    *   Evaluates rules in order, using the first matching rule's decision.
    *   Denies access if no rules match.
*   Implement rule matching:
    *   A rule without a 'user' field matches any user.
    *   A rule without a 'schema' field matches any schema name.
*   Update the test resource `security-config-file-with-unknown-rules.json` to use a different field name than 'schemas' for unknown rule sections.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.