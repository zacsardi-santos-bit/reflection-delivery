## Description

Python has a special built-in read-only constant that represents whether the interpreter is running without optimizations. The language specification explicitly forbids any code from reassigning, binding, or shadowing this name — Python's own compiler rejects these as syntax errors. Similarly, deleting this name was deprecated and then fully removed in a specific release of Python; older Python versions permitted it.

The parser's semantic syntax checker currently does not detect any of these violations. Code that Python itself would reject as a syntax error passes through without any diagnostic.

## Expected Behavior

- Any statement that binds this protected name in an assignment-like context should produce a clear syntax error. This covers:
  - Direct variable assignment and tuple-unpacking assignment
  - Defining a function or class with that name
  - Using it as a function parameter or type parameter (including in classes and type aliases)
  - Importing into it (direct import, aliased import, from-import)
  - Binding it via a with-statement, except-handler, match pattern, or type alias name

- Deleting this name should produce a version-specific error when the configured target Python version is new enough that the deletion syntax was already removed. The error message should identify the target Python version and name the release in which the syntax was removed.

- Reading this name as a value must remain valid with no error.

- Imports where the identifier is used as a *source* name (a module name or the thing being imported) rather than the *bound* name are valid — only the binding side matters.

- Deleting the name when targeting an older Python version (before the syntax was removed) must remain valid.

## Why This Matters

The parser should surface these violations rather than silently accepting them, keeping the linter's behavior consistent with how Python's own compiler handles these cases and helping developers catch mistakes early.

The test infrastructure also needs updating so that the configured Python target version is forwarded into the semantic checker, enabling version-sensitive diagnostics to work correctly across different configured versions.
