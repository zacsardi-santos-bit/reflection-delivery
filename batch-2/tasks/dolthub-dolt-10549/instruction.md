I'm working with Dolt and I've noticed that when I check out a different branch, tables I've explicitly marked as ignored can be silently overwritten by the version on the target branch.

*   An exported error value named ErrCheckoutWouldOverwriteIgnoredTables must be defined in the package go/libraries/doltcore/env/actions; this value is returned (or wrapped) when a checkout is blocked because ignored tables would be overwritten.

*   The checkout command (both CLI dolt checkout and SQL CALL DOLT_CHECKOUT()) must accept a --no-overwrite-ignore flag. When this flag is present and one or more ignored tables differ between the current branch and the target branch, the checkout must be aborted and the error message must contain the substring "ignored tables would be overwritten by checkout" as well as the names of the specific tables that would be overwritten.

*   The checkout command (both CLI and SQL) must accept a --overwrite-ignore flag. When this flag is present, checkout proceeds and ignores tables are overwritten — this is identical to the default behavior but expressed explicitly.

*   The --no-overwrite-ignore and --overwrite-ignore flags are mutually exclusive. If both are supplied in the same checkout invocation, the operation must fail with an error message containing the word "mutually exclusive".

*   Default checkout behavior (no flag) must remain unchanged: ignored tables are overwritten and the checkout succeeds.

*   When --no-overwrite-ignore is set, checkout must succeed (not be blocked) if the ignored table is identical on both the current and target branches — only actual differences trigger the abort.

*   When --no-overwrite-ignore is set, checkout must succeed when an ignored table exists only on the target branch (there is no local version of the table to protect).

*   When --no-overwrite-ignore is set, checkout must succeed when an ignored table exists only on the current branch and is absent from the target branch (the target cannot overwrite something it does not have).

*   When both --force and --no-overwrite-ignore are supplied, the --force flag must not override the --no-overwrite-ignore protection: if an ignored table would be overwritten, the checkout must still be aborted with the appropriate error.

*   Creating a new branch from the current HEAD using -b (or -B) combined with --no-overwrite-ignore must always succeed and must never trigger the ignored-table overwrite check, because the new branch starts from the same commit.

*   When multiple ignored tables exist and only some of them differ between branches, the error output must list only the tables that would actually be overwritten; tables that are identical on both branches must not appear in the error message.

*   The --no-overwrite-ignore and --overwrite-ignore flags must work when checkout is invoked via the SQL stored procedure interface (CALL DOLT_CHECKOUT()), including when combined with the --move option.


*   Interface details: Type: Variable
Name: ErrCheckoutWouldOverwriteIgnoredTables
Location: go/libraries/doltcore/env/actions/errors.go
Signature: var ErrCheckoutWouldOverwriteIgnoredTables = goerrors.NewKind("The following ignored tables would be overwritten by checkout:\n\t%s\nPlease move or remove them before you switch branches.\nUse --overwrite-ignore to force.\n")
Description: Exported error-kind value (type *goerrors.Kind from gopkg.in/src-d/go-errors.v1) that is returned when a checkout with --no-overwrite-ignore is attempted and one or more ignored tables differ between the current branch and the target branch. Must be a *goerrors.Kind because other code in the codebase calls .Is(err) on this value directly. Errors of this kind are created with ErrCheckoutWouldOverwriteIgnoredTables.New(tableNamesList). The test references this value as actions.ErrCheckoutWouldOverwriteIgnoredTables to assert the expected error kind.

Type: Function
Name: MoveWorkingSetToBranch
Location: go/libraries/doltcore/sqle/dprocedures/dolt_checkout_helpers.go
Signature: MoveWorkingSetToBranch(ctx *sql.Context, brName string, force bool, isNewBranch bool, overwriteIgnore bool) error
Description: Existing exported function that moves the working set to a target branch during CLI-style checkout. A new boolean parameter overwriteIgnore is added; when false, the function checks whether any ignored tables would be overwritten and returns ErrCheckoutWouldOverwriteIgnoredTables if so. All callers of this function must be updated to pass the new overwriteIgnore argument.

Type: Function
Name: CheckOverwrittenIgnoredTables
Location: go/libraries/doltcore/env/actions/checkout.go
Signature: CheckOverwrittenIgnoredTables(ctx context.Context, roots doltdb.Roots, branchRoot doltdb.RootValue, overwriteIgnore bool) error
Description: New exported helper function that determines whether any ignored tables in the working set would be overwritten by checking out branchRoot. When overwriteIgnore is true it returns nil immediately. When overwriteIgnore is false it compares each ignored working-set table against the corresponding table on branchRoot, collecting only those where branchRoot's version differs (and is non-empty), and returns ErrCheckoutWouldOverwriteIgnoredTables.New(joinedTableNames) if any are found.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.