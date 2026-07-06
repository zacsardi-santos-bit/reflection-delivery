Implement support for the PostgreSQL 'varbit' type in the sqlc code generator to ensure correct Go type mapping. Update the `postgresType` function to handle this type and return the appropriate Go type when using the SQLPackagePGXV4 driver configuration.

Requirements:

*   Update the `postgresType` function located in `internal/codegen/golang/postgresql_type.go` to:
    *   Recognize the PostgreSQL 'varbit' (variable-length bit string) column type.
    *   Return "pgtype.Varbit" when the SQL package is `SQLPackagePGXV4`.
*   Ensure the `postgresType` function signature is:
    *   `postgresType(req *plugin.CodeGenRequest, col *plugin.Column) string`
    *   This function maps a PostgreSQL column type to its corresponding Go type string.
*   Use the `SQLPackagePGXV4` constant from `internal/codegen/golang/` to identify the pgx v4 SQL package.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.