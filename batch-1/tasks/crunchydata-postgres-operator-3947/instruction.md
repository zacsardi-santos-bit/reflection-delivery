Update the embedded shell scripts in the postgres-operator project to use modern and robust shell scripting practices. Ensure that the scripts handle process substitution failures gracefully and tolerate individual command failures without aborting the overall process.

*   Update the pgbackrest reload command script in `internal/pgbackrest/config.go`:
    *   Use double-bracket conditionals for all file modification time comparisons.
    *   Use `<(:||:)` for all process substitutions.
    *   Use `exec {fd}>&- && exec {fd}<> <(:||:)` when resetting the file descriptor.

*   Update the pgbouncer reload command script in `internal/pgbouncer/config.go`:
    *   Use double-bracket conditionals for directory file modification checks.
    *   Use `<(:||:)` for process substitutions.
    *   Replace `|| true` with `||:` in the while loop condition.

*   Update the postgres TLS certificate monitoring script in `internal/postgres/config.go`:
    *   Use double-bracket conditionals for directory file modification time comparisons.
    *   Use `<(:||:)` for process substitutions.
    *   Replace `|| true` with `||:` in the while loop condition.

*   Update the postgres startup/initialization script in `internal/postgres/config.go`:
    *   Use double-bracket conditionals for all file and directory existence tests.
    *   Use `[[ "${current}" == "${desired}" ]]` in the safelink helper function.
    *   Append `||:` inside command substitutions for `id -u`, `id -G`, `command -v postgres`, `postgres --version`, and `realpath` of the `pg_wal` directory.

*   Update the standalone pgadmin reload script in `internal/controller/standalone_pgadmin/pod.go`:
    *   Use double-bracket conditionals for cluster file modification time comparisons, process directory checks, and version comparisons.
    *   Use `<(:||:)` for process substitutions.
    *   Replace `|| true` with `||:` in the while loop condition.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.