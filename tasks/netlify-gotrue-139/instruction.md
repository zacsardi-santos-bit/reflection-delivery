Implement a utility function to determine which database columns should be excluded from an update operation, based on a struct's `db` tag annotations. Ensure the function accurately identifies columns to exclude and handles errors for invalid column names.

*   Implement a package-private function named `getExcludedColumns` in the `storage` package, located in the file `storage/dial.go`.
    *   Signature: `getExcludedColumns(model interface{}, includeColumns ...string) ([]string, error)`
*   Ensure the function accepts:
    *   A struct value as the first argument.
    *   A variadic list of database column names (matching `db` struct tag values) as subsequent arguments.
*   When called with valid column names:
    *   Return a string slice containing all other `db`-tagged column names from the struct that are not in the provided list.
    *   Return a nil error.
*   Ensure the returned slice does not contain any of the column names passed in the include list.
*   When called with a column name that does not correspond to any `db` struct tag value on the given struct:
    *   Return a non-nil error.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.