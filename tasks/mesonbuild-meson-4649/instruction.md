Implement a new built-in function `summary()` in the Meson build system to provide a structured overview of build configurations at the end of the setup phase. This function should allow project authors to register key-value configuration information throughout the build script, supporting both individual entries and dictionaries, with optional section names. Ensure the summary is formatted and aligned, with subproject summaries appearing before the main project.

Requirements:

*   Register the `summary()` function in the Interpreter's function dispatch table.
    *   Accept 1-3 positional arguments: (dict), (key, value), (section, dict), or (section, key, value).
    *   Raise an error if more than 3 arguments are provided.
*   Handle argument types:
    *   Single argument must be a dictionary.
    *   Two arguments: first must be a string; if second is a dictionary, they are (section, values); otherwise, section is `''` and pair is `{args[0]: args[1]}`.
    *   Three arguments: first two must be strings.
    *   Raise an interpreter error for type mismatches.
*   Implement `bool_yn` keyword (default False) to render booleans as 'YES'/'NO' when True.
*   Ensure summary values are strings, integers, or booleans; raise an error for other types.
*   Accumulate entries across multiple `summary()` calls within the same section.
    *   Raise an error if the same key is added twice to the same section.
*   Format output at the end of the setup phase:
    *   Subproject summaries first (alphabetically), then main project.
    *   Each summary block starts with a header line containing the project name and version.
    *   Right-align keys with padding based on the longest key.
    *   For list values, display the first item inline with the key; indent subsequent items.
*   Exclude failed subprojects from the summary output.
    *   Silently skip subprojects declared with `required: false` if they fail.
*   Register `summary()` in the AstInterpreter's function map to prevent static analysis failures.
*   Implement the `Summary` class in `mesonbuild/interpreter.py`:
    *   `__init__(self, project_name: str, project_version: str) -> None`
    *   `add_section(self, section: str, values: dict, kwargs: dict) -> None`
        *   Raise `InterpreterException` for duplicate section/key pairs or invalid value types.
    *   `dump(self) -> None` to output formatted summary.
*   Implement `func_summary`, `summary_impl`, and `_print_summary` methods in `Interpreter`.
*   Maintain a `summary` dict attribute in `Interpreter` for storing summaries.
*   Add `test_summary` method to `AllPlatformTests` in `run_unittests.py`:
    *   Use directory '74 summary', call `self.init()`, and verify formatted summary output.
*   Ensure `test cases/unit/74 summary/subprojects/sub2/meson.build` exists with a failing subproject.
*   Ensure `test cases/unit/74 summary/meson.build` and `test cases/unit/74 summary/subprojects/sub/meson.build` are provided for testing.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.