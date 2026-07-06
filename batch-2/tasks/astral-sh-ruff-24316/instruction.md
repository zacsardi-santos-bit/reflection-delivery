I'm working with a linter rule that flags mutable default values passed to dictionary-from-keys calls and offers an autofix that rewrites the call as a dictionary comprehension.

*   The RUF024 rule must detect `dict.fromkeys('ABC', list(key))` as a violation even when `key` refers to an existing variable in the outer scope, not just when the second argument is a bare mutable literal.

*   The autofix for RUF024 must use `fresh_binding_name` to select the comprehension loop variable, trying the base name `key` first, then `key_0`, `key_1`, etc. in order, returning the first name that is not already bound in the current scope.

*   When both `key` and `key_0` are already in scope, the comprehension fix must use `key_1` as the loop variable, producing output of the form `{key_1: list(key) for key_1 in 'ABC'}`.

*   The `generate_dict_comprehension` function must accept a `SemanticModel` parameter (in addition to keys, value, and generator) so it can consult the semantic model when choosing the loop variable name.

*   The snapshot file `crates/ruff_linter/src/rules/ruff/snapshots/ruff_linter__rules__ruff__tests__RUF024_RUF024.py.snap` must be updated to include the new RUF024 diagnostic for line 39, with the message `Do not pass mutable objects as values to \`dict.fromkeys\`` and the fix `{key_1: list(key) for key_1 in "ABC"}`.

*   The fix must continue to be marked as an unsafe fix (indicated by the `[*]` marker and the `note: This is an unsafe fix and may change runtime behavior` message).


*   Interface details: Type: Function
Name: generate_dict_comprehension
Location: crates/ruff_linter/src/rules/ruff/rules/mutable_fromkeys_value.rs
Signature: generate_dict_comprehension(keys: &Expr, value: &Expr, generator: Generator, semantic: &SemanticModel<'_>) -> String
Description: Generates a dictionary comprehension string from a dict.fromkeys call. Accepts the semantic model as a fourth parameter in order to select a fresh loop variable name that does not shadow any existing binding in the current scope.

Type: Function
Name: fresh_binding_name
Location: crates/ruff_linter/src/rules/ruff/rules/mutable_fromkeys_value.rs
Signature: fresh_binding_name(semantic: &SemanticModel<'_>, base: &str) -> Name
Description: Returns a fresh binding name derived from `base` that does not shadow an existing non-builtin symbol in the current semantic scope. First tries `base` itself; if unavailable, tries `base_0`, `base_1`, etc. in order until a free name is found.

Type: Snapshot file
Name: ruff_linter__rules__ruff__tests__RUF024_RUF024.py.snap
Location: crates/ruff_linter/src/rules/ruff/snapshots/ruff_linter__rules__ruff__tests__RUF024_RUF024.py.snap
Description: Snapshot file for the RUF024 rule test. Must be updated to include the new diagnostic for `dict.fromkeys("ABC", list(key))` at line 39, with the fix showing `{key_1: list(key) for key_1 in "ABC"}` (using `key_1` because both `key` and `key_0` are already in scope). The diagnostic must be appended after the existing snapshot content.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.