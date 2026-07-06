## Description

The async zero-sleep linting rule, which detects and auto-fixes calls to sleep with a zero duration, generates incorrect import statements in its autofix suggestions. The component being imported is a submodule of the async library, not a plain attribute, so the only correct way to import it is as a direct module import. However, the current autofix generates a destructured "from" import instead.

This means that after applying the suggested fix, the generated code may fail to run because the import style is wrong for a submodule.

## Expected Behavior

- The autofix should generate a direct submodule import, not a destructured import.
- The replacement call should always use the fully-qualified module path, not a short unqualified reference.
- When the sleep function has been imported under an alias, the autofix should still correctly use the fully-qualified submodule path in the replacement, not a reference derived from the alias.

## Why This Matters

Autofixes that produce broken code undermine user trust in the linter. Anyone who applies the suggested fix will end up with code that fails at import time, requiring a second manual correction. The fix should generate import statements that are syntactically and semantically valid.
