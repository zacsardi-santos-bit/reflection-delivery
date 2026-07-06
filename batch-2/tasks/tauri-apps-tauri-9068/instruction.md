Fix the bug in the Access Control List (ACL) resolution system where scopes from multiple capabilities targeting different windows are incorrectly merged. Ensure that each permission grant for a command produces its own resolved entry, preserving its scope and window constraints.

*   Update the `Resolved` struct:
    *   Change `allowed_commands` and `denied_commands` to `BTreeMap<String, Vec<ResolvedCommand>>`, keyed by command name string.
    *   Remove the composite key type combining command name and execution context.
    *   Ensure `command_scope` uses sequential integer keys starting at 1.

*   Modify the `ResolvedCommand` struct:
    *   Include a `context: ExecutionContext` field, moved from the old composite map key.
    *   Rename the `scope` field to `scope_id`, maintaining its type as `Option<ScopeKey>`.
    *   Ensure the `fmt::Debug` implementation outputs fields in the order: `context`, `windows`, `webviews`, `scope_id`.

*   Remove the `CommandKey` struct:
    *   Eliminate the composite struct `CommandKey { name: String, context: ExecutionContext }`.

*   Implement sequential scope identifiers:
    *   Assign sequential integers as scope identifiers during resolution, starting from 1.
    *   Avoid deriving identifiers from hashing scope content.

*   Ensure independent command entries:
    *   For commands granted by multiple capabilities or permissions, create separate `ResolvedCommand` entries for each grant.
    *   Prevent merging of scopes across different grants.

*   Verify multi-window capability resolution:
    *   Ensure commands shared between `main` and `external` windows appear as separate entries.
    *   Assign sequential `scope_id` values to `main` window entries, and `scope_id: None` to `external` window entries.
    *   Accumulate deny rules in the `global_scope` from both capabilities.

*   Update the `resolve_command` function:
    *   Change the signature to operate on `&mut BTreeMap<String, Vec<ResolvedCommand>>`.
    *   For each context (Local and/or Remote), push a new `ResolvedCommand` entry into the Vec for the command name.

*   Add new fixture files for multi-window testing:
    *   `cap-main.json` for the "main" window with specific fs permissions.
    *   `cap-external.toml` for the "external" window with limited permissions.
    *   `required-plugins.json` listing the required plugin `["fs"]`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.