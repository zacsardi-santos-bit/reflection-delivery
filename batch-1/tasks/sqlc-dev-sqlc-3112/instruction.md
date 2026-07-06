Implement a fix for the sqlc code generator to correctly handle optional named parameters nested inside SQL function calls. Ensure that these parameters are included in the generated code with the appropriate types for both MySQL and PostgreSQL targets.

*   Modify the `resolveCatalogRefs` function in `internal/compiler/resolve.go`:
    *   Ensure that named optional parameters (e.g., `sqlc.narg`) appearing as arguments to SQL functions with unknown return types are included in the generated output with type 'any'.
    *   For MySQL, use `interface{}` for parameters whose types cannot be inferred and appropriate nullable types (e.g., `sql.NullInt32`) for those that can.
    *   For PostgreSQL, use `sql.NullString` for string parameters with explicit casts and `sql.NullInt32` for nullable integer parameters.

*   Update the SQL string generation:
    *   For MySQL, replace `sqlc.narg()` calls with `?` placeholders, ensuring correct placement within nested function calls like `COALESCE` and `CONCAT`.
    *   For PostgreSQL, replace `sqlc.narg()` calls with numbered placeholders (e.g., `$1`, `$2`), maintaining order and including explicit casts (e.g., `$1::text`).

*   Ensure the following end-to-end test data directories exist with correct input SQL and expected output files:
    *   `internal/endtoend/testdata/params_in_nested_func_isolated/mysql/`
        *   Include `query.sql`, `schema.sql`, `sqlc.yaml`, and generated files under `db/`.
    *   `internal/endtoend/testdata/params_in_nested_func/mysql/`
        *   Replicate the content of the isolated MySQL variant.
    *   `internal/endtoend/testdata/params_in_nested_func/postgresql/`
        *   Include PostgreSQL-specific `query.sql`, `schema.sql`, `sqlc.yaml`, and generated files under `db/`.

*   Ensure the MySQL `GetGroupsParams` struct includes `GroupName` of type `interface{}` and `GroupId` of type `sql.NullInt32`.
*   Ensure the PostgreSQL `GetGroupsParams` struct includes `GroupName` of type `sql.NullString` and `GroupId` of type `sql.NullInt32`.
*   Use `int32` for the PostgreSQL `Routergroup` model's `groupId` field and `uint32` for the MySQL variant.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.