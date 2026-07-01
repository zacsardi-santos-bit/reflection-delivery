Implement functionality to enforce case-sensitive collation in MySQL and MSSQL databases and support branch names containing emoji and extended Unicode characters in Gitea.

*   Update the `CheckCollations` function in `models/db/collation.go`:
    *   Accept an `xorm.Engine` instance and return a `CheckCollationsResult` struct pointer and an error.
    *   Include fields in `CheckCollationsResult`:
        *   `DatabaseCollation` (string): Current database collation.
        *   `ExpectedCollation` (string): Expected collation.
        *   `AvailableCollation`: Set of available collation strings.
        *   `InconsistentCollationColumns` ([]string): Columns with inconsistent collation in "tablename.columnname" format.
    *   Implement `IsCollationCaseSensitive(collation string) bool`:
        *   For MySQL, return true for 'utf8mb4_bin' and collations matching '*_as_cs'.
        *   For MSSQL, return true for 'Latin1_General_CS_AS'.
    *   Implement `CollationEquals(a, b string) bool`:
        *   For MySQL, treat 'utf8mb4_' prefix as optional.
        *   For MSSQL, require exact matches.

*   Ensure `CheckCollations` reports:
    *   `DatabaseCollation` is case-sensitive when `setting.Database.CharsetCollation` is empty.
    *   `ExpectedCollation` equals `DatabaseCollation`.
    *   `AvailableCollation` is non-empty.
    *   `InconsistentCollationColumns` is empty.

*   Implement `ConvertDatabaseTable` function in `models/db/convert.go`:
    *   Convert all tables to use the collation specified by `setting.Database.CharsetCollation`.
    *   If `CharsetCollation` is empty, use a case-sensitive default.
    *   Ensure `CheckCollations` reports no `InconsistentCollationColumns` post-conversion.
    *   Report any tables with different collations in `InconsistentCollationColumns`.

*   Ensure newly created tables use a case-sensitive collation by default:
    *   Allow insertion of rows differing only in case under a UNIQUE constraint without conflict.

*   Update the branch creation API endpoint:
    *   Accept branch names with UTF-8 extended characters, including emoji.
    *   Return HTTP 201 Created upon successful branch creation.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.