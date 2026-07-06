Implement a fix for the FAST002 lint rule to correctly handle FastAPI dependency parameters with default values. Ensure the autofix extracts default values from dependency calls and places them as standalone parameter defaults, while preserving non-default keyword arguments.

*   Update the FAST002 lint rule to:
    *   Detect non-annotated FastAPI dependency parameters in `FAST002_1.py`.
    *   Raise diagnostics for parameters with positional defaults, keyword `default=` arguments, and mixed forms.
    *   Extract default values from dependency calls and place them as standalone defaults in the form `param: Annotated[type, Dep()] = default_value`.
    *   Preserve non-default keyword arguments inside the dependency call.
    *   Ensure valid Python syntax, especially regarding parameter ordering rules.

*   Modify the Rust implementation in `fastapi_non_annotated_dependency.rs` to:
    *   Implement logic for extracting default values from route-parameter dependency calls (Query, Path, Body, Cookie, Header, File, Form).
    *   Retain full call expressions for Depends and Security inside the annotation.

*   Update the test registration in `mod.rs` to:
    *   Register test cases for `FAST002_0.py` and `FAST002_1.py` in both standard Python and Python-3.8 test functions.

*   Rename and adjust fixture files:
    *   Rename `FAST002.py` to `FAST002_0.py`.
    *   Reorder parameters in `FAST002_0.py` so non-default parameters precede those with defaults.
    *   Ensure `FAST002_1.py` includes handlers with various forms of defaults.

*   Ensure snapshot files reflect updated test outputs:
    *   `ruff_linter__rules__fastapi__tests__fast-api-non-annotated-dependency_FAST002_0.py.snap` and its py38 variant must show reordered parameters and corrected autofixes.
    *   `ruff_linter__rules__fastapi__tests__fast-api-non-annotated-dependency_FAST002_1.py.snap` and its py38 variant must show diagnostics and fixes for all handler scenarios.

*   For Python 3.9+, import `Annotated` from `typing`; for Python 3.8, import from `typing_extensions`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.