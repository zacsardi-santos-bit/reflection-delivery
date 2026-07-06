I need a way to reset the local memory system to a clean slate from the command line.

*   The CLI binary must support a 'debug clear-memories' subcommand that exits successfully (exit code 0) and prints output to stdout that contains the substring "Cleared memory state".

*   When the 'debug clear-memories' command runs, it must delete all rows from the 'stage1_outputs' table in the state database.

*   When the 'debug clear-memories' command runs, it must delete all rows from the 'jobs' table where the 'kind' column equals 'memory_stage1' or 'memory_consolidate_global'.

*   When the 'debug clear-memories' command runs, it must update all rows in the 'threads' table where 'memory_mode' is 'enabled', setting 'memory_mode' to 'disabled'.

*   When the 'debug clear-memories' command runs, it must remove the 'memories' directory (and all its contents) located under CODEX_HOME if it exists.

*   The command must respect the CODEX_HOME environment variable to locate both the state database and the memories directory on the filesystem.

*   The 'state_db_path' function must be publicly exported from the 'codex_state' crate and return the path to the SQLite database file given a home directory path.


*   Interface details: Type: CLI Subcommand
Name: debug clear-memories
Location: codex-rs/cli/src/main.rs
Signature: codex debug clear-memories
Description: A hidden subcommand under the existing 'debug' command group. When invoked, it resets local memory state by clearing database records and the memories directory on disk, then prints a success message to stdout.

Type: Function
Name: state_db_path
Location: codex-rs/state/src/lib.rs (or re-exported from codex-rs/state/src/)
Signature: state_db_path(home: &Path) -> PathBuf
Description: Public function in the codex_state crate that returns the path to the SQLite database file given a home directory path. Must be exported from the codex_state crate's public API so it can be imported by external crates and tests.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.