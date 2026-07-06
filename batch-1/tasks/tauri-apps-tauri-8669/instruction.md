Restructure the ACL permission resolution system to organize global scope permissions by plugin. Ensure each plugin's permissions are isolated and correctly attributed in the resolved global scope.

*   Update the `Resolved` struct in `core/tauri-utils/src/acl/resolved.rs`:
    *   Change the `global_scope` field from `ResolvedScope` to `BTreeMap<String, ResolvedScope>`.
    *   Each key in the `BTreeMap` should be a plugin name (as a string).
    *   Each value should be the `ResolvedScope` containing the allow and deny entries for that plugin.

*   Implement logic to resolve ACL permissions:
    *   Group global scope permissions by the associated plugin name.
    *   Insert these permissions into the `global_scope` map under the plugin's name as a key.
    *   Ensure allow and deny scope items for each plugin are merged into a single `ResolvedScope` under the plugin's key.

*   Handle cases with no global scope permissions:
    *   Ensure `global_scope` is an empty `BTreeMap` when no global scope permissions exist.
    *   The Debug output should serialize this as `{}`.

*   Ensure correct Debug representation:
    *   When global scope permissions exist, represent the `global_scope` with plugin names as quoted string keys.
    *   Example Debug output: `{ "fs": ResolvedScope { allow: [...], deny: [...] } }`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.