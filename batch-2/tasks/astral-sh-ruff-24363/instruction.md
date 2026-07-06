I have a lint rule in my linter project that flags string literals used directly inside exception raise statements.

*   When the EM101/EM102/EM103 fix generator chooses a variable name for extracting a string literal from a raise statement, it must first check whether 'msg' is already bound in the current scope. If 'msg' is available, use it; otherwise try 'msg_0', then 'msg_1', etc., until an available name is found.

*   For the regression test fixture function (a function that defines 'msg' at the top and raises with a string literal inside a try block), the EM101 violation must be detected and the auto-fix must use 'msg_0' as the extracted variable name — not 'msg' — to avoid shadowing the existing binding.

*   For the pre-existing 'f_msg_defined' fixture case (a function that defines 'msg = "hello"' and then raises with a string literal), the fix must now use 'msg_0' instead of 'msg'. The defaults and custom snapshot files must reflect this updated behavior.

*   The defaults snapshot file ('ruff_linter__rules__flake8_errmsg__tests__defaults.snap') must include the new EM101 violation entry for the regression test fixture at line 118 of EM.py, with the fix showing 'msg_0 = "!"' and 'raise RuntimeError(msg_0)'.

*   The custom snapshot file ('ruff_linter__rules__flake8_errmsg__tests__custom.snap') must be updated so that the f_msg_defined case and its nested scope variant both use 'msg_0' instead of 'msg' in the fix output.

*   The existing variable in scope (the original 'msg' binding) must remain completely unchanged after the fix is applied; the fix must only insert the new 'msg_0' binding before the raise and replace the string literal with 'msg_0' in the raise expression.


*   Interface details: ## Snapshot Files

The three snapshot tests (`defaults`, `custom`, `string_exception`) compare the linter's output for the fixture file `crates/ruff_linter/resources/test/fixtures/flake8_errmsg/EM.py` against stored snapshot files. These snapshots must be updated to reflect the new collision-avoiding variable naming behavior.

### Snapshot: defaults

File: `crates/ruff_linter/src/rules/flake8_errmsg/snapshots/ruff_linter__rules__flake8_errmsg__tests__defaults.snap`

This snapshot must include:
1. Updated fix output for the pre-existing `f_msg_defined` case: the fix must now use `msg_0` instead of `msg` (because `msg` is already defined in that function's scope).
2. New EM101 violation entry for the added `f()` function (the regression test fixture at the end of `EM.py`), with:
   - Violation at `EM.py:118:28` (`raise RuntimeError("!")`)
   - Fix showing `msg_0 = "!"` and `raise RuntimeError(msg_0)` (not `msg`)

### Snapshot: custom

File: `crates/ruff_linter/src/rules/flake8_errmsg/snapshots/ruff_linter__rules__flake8_errmsg__tests__custom.snap`

This snapshot must include:
1. Updated fix output for the pre-existing `f_msg_defined` case: fix must use `msg_0` instead of `msg`.
2. Updated fix output for the nested scope case inside `f_msg_defined`: fix must use `msg_0` instead of `msg`.

### Snapshot: string_exception

File: `crates/ruff_linter/src/rules/flake8_errmsg/snapshots/ruff_linter__rules__flake8_errmsg__tests__string_exception.snap`

This snapshot may not require changes if the `string_exception` test configuration does not exercise the cases where a collision occurs. Verify whether this snapshot file needs updating by running the tests after implementing the fix.

## Implementation

The fix generation logic for the EM101/EM102/EM103 rules lives in:
`crates/ruff_linter/src/rules/flake8_errmsg/rules/string_in_exception.rs`

The collision-avoiding variable name selection must follow this pattern:
- First candidate: `msg`
- If `msg` is already bound in the current semantic scope, try `msg_0`
- If `msg_0` is also taken, try `msg_1`, and so on
- Use the first available (unbound) candidate name

This logic should consult the semantic model to determine whether a name is available in the current scope.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.