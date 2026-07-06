Implement a semantic syntax checker for the Python parser that identifies illegal bindings of the `__debug__` identifier, consistent with Python's own compiler behavior. Ensure the checker also handles version-specific syntax for deletion of `__debug__`.

*   Update the `ParseOptions` struct in `crates/ruff_python_parser/src/parser/options.rs`:
    *   Implement a public method `target_version(&self) -> PythonVersion` to return the configured target Python version.

*   Modify the semantic syntax checker to:
    *   Emit an error message 'cannot assign to `__debug__`' for any binding of `__debug__` in assignment contexts, including:
        *   Direct assignment, tuple unpacking, function and class definitions, function and type parameters, import targets, with-statement bindings, except-handler names, match-pattern captures, and type alias names.
    *   Emit an error message 'cannot delete `__debug__` on Python X.Y (syntax was removed in 3.9)' when deleting `__debug__` if the target Python version is 3.9 or later.
    *   Allow reading `__debug__` as a value without errors.
    *   Allow imports where `__debug__` is used as a source name or is aliased to another name without errors.
    *   Allow deleting `__debug__` if the target Python version is earlier than 3.9.

*   Update the test infrastructure:
    *   Add a `python_version: PythonVersion` field to `TestContext` in `crates/ruff_python_parser/tests/fixtures.rs`.
    *   Implement a builder method `with_python_version(mut self, python_version: PythonVersion) -> Self`.
    *   Ensure `SemanticSyntaxContext::python_version(&self) -> PythonVersion` returns `self.python_version`.
    *   Pass `options.clone()` to `parse_unchecked` in test functions and construct `SemanticSyntaxCheckerVisitor` with `TestContext::default().with_python_version(options.target_version())`.

*   Create Python test fixture files with specified content:
    *   Error fixtures in `crates/ruff_python_parser/resources/inline/err/`:
        *   `debug_shadow_class.py`, `debug_shadow_function.py`, `debug_shadow_import.py`, `debug_shadow_match.py`, `debug_shadow_try.py`, `debug_shadow_type_alias.py`, `debug_shadow_with.py`, `del_debug_py39.py`, `write_to_debug_expr.py`.
    *   Valid fixtures in `crates/ruff_python_parser/resources/inline/ok/`:
        *   `debug_rename_import.py`, `del_debug_py38.py`, `read_from_debug.py`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.