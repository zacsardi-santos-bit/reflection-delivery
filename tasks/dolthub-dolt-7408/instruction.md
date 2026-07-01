Enhance the `dolt log` command by implementing a `--stat` flag that provides a concise summary of changes made in each commit, such as tables added, dropped, or modified, and the number of rows affected. Ensure compatibility with existing log formats and handle merge commits appropriately.

*   Implement the `--stat` flag for the `dolt log` command to display a diffstat summary below each commit's header.
    *   For a new table added in a commit, include a line: ` X added`.
    *   For a table dropped in a commit, include a line: ` X deleted`.
    *   For rows inserted into table X, include a line: ` X | N +` and a summary: ` 1 tables changed, N rows added(+), 0 rows modified(*), 0 rows deleted(-)`.
    *   For rows updated in table X, include a line: ` X | N *` and a summary: ` 1 tables changed, 0 rows added(+), N rows modified(*), 0 rows deleted(-)`.
    *   For rows deleted from table X, include a line: ` X | N -` and a summary: ` 1 tables changed, 0 rows added(+), 0 rows modified(*), N rows deleted(-)`.
*   Exclude diffstat output for merge commits (those with more than one parent).
    *   Ensure output for merge commits includes only the standard commit metadata: commit hash, author, date, an empty line, and the message, totaling 5 lines.
*   Allow the `--stat` flag to be used with the `--oneline` option.
    *   When used across two commits (one adding a table, one inserting a row), ensure the output has exactly 6 lines, with diffstat lines directly following each oneline commit entry.
*   Ensure the diffstat output format may include ANSI color escape sequences, which consumers should handle or strip to obtain plain text.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.