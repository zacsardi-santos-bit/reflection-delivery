Fix the DAO code generation tool to address two issues: ensure all managed file paths are reported, and respect per-table overwrite settings independently. Implement the following changes:

*   Update the `generateDaoIndex` function in `cmd/gf/internal/cmd/gendao/gendao_dao.go`:
    *   Ensure that the target file path is appended to `in.generatedFilePaths.DaoFilePaths` unconditionally, before checking the `OverwriteDao` setting.
    *   Use the signature: `generateDaoIndex(in generateDaoIndexInput)`.

*   Modify the `Dao` method in `cmd/gf/internal/cmd/gendao/gendao.go` (or `cmd/gf/internal/cmd/cmd_gen_dao.go`):
    *   Ensure it processes each table entry independently using its own configuration, including the `overwriteDao` setting.
    *   Use the signature: `(c CGenDao) Dao(ctx context.Context, in CGenDaoInput) (out *CGenDaoOutput, err error)`.
    *   When `overwriteDao` is `false` for a table, preserve the existing DAO index file without overwriting.
    *   When `overwriteDao` is `true`, regenerate the DAO index file with new contents.

*   Ensure the `CGenDao{}.Dao` method generates files at these paths for each table:
    *   `dao/internal/{table}.go`
    *   `dao/{table}.go`
    *   `model/do/{table}.go`
    *   `model/entity/{table}.go`

*   Verify the test fixture data exists in `cmd/gf/internal/cmd/testdata/issue/2616/`:
    *   Include `sql1.sql` and `sql2.sql` for table DDLs.
    *   Ensure `config.yaml` has two DAO generation entries, with `overwriteDao: false` for user1 (group: sys) and `overwriteDao: true` for user2 (group: book).
    *   Confirm pre-existing DAO files `dao/user_1.go` and `dao/user_2.go` contain the comment "// I am not overwritten."
    *   Check pre-existing files in `dao/internal/`, `model/do/`, and `model/entity/` for both tables.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.