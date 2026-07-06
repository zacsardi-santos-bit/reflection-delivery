I'm running into an issue with my linter configuration.

*   When `lint.isort.required-imports` contains an unaliased `from collections.abc import Set` entry AND the rule `PYI025` (unaliased-collections-abc-set-import) is also enabled, the linter must detect this as a fatal configuration conflict and exit with code 2 before performing any linting.

*   The conflict error output must be written to stderr and must contain the exact message: "Required import `from collections.abc import Set` specified in `lint.isort.required-imports` (I002) conflicts with `unaliased-collections-abc-set-import` (PYI025), which requires this import to be aliased as `AbstractSet`." followed by the help text: "Either alias the required import (`from collections.abc import Set as AbstractSet`), or disable PYI025."

*   When a conflict is detected between the two rules, stdout must be empty and the exit code must be 2 (not 1).

*   When `lint.isort.required-imports` contains `from collections.abc import Set as AbstractSet` (aliased as AbstractSet) and both `I002` and `PYI025` are enabled, no conflict must be detected. The linter must run normally, exit with code 1, and report the missing required import via I002.

*   When `lint.isort.required-imports` contains an unaliased `from collections.abc import Set` but `PYI025` is NOT among the enabled rules, no conflict must be detected. The linter must run normally, exit with code 1, and report the missing required import via I002.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.