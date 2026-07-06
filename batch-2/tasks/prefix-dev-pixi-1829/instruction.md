Update the Python environment management dependency in your codebase to reflect the new module name and ensure all references are consistent. Implement a cleanup flag in the test runner script to manage project environments before testing.

*   Modify the workspace-level Cargo.toml:
    *   Replace the `uv-toolchain` dependency with `uv-python` at git tag 0.2.37 from https://github.com/astral-sh/uv.
    *   Remove the `uv-toolchain` key entirely.
    *   Update all other `uv-family` crate entries from tag 0.2.18 to tag 0.2.37.

*   Update Rust source files:
    *   Change all imports from `uv_toolchain` to `uv_python`.
    *   Examples include changing `uv_toolchain::Interpreter` to `uv_python::Interpreter` and `uv_toolchain::PythonEnvironment` to `uv_python::PythonEnvironment`.

*   Modify the pixi binary's Cargo.toml:
    *   Replace `uv-toolchain = { workspace = true }` with `uv-python = { workspace = true }`.

*   Enhance the test runner script `tests/run_all_examples.py`:
    *   Update the `run_test_in_subfolders` function signature to include `run_clean: bool = False`.
    *   When `run_clean` is `True`, execute `[pixi_exec, 'clean', '--manifest-path', manifest_path]` before running tests.
    *   If the clean command fails (non-zero return code), append the folder name to `results.failed` and skip the test.

*   Update the `__main__` block in `tests/run_all_examples.py`:
    *   Add a `--clean` argument to the argument parser with `action='store_true'`.
    *   Pass `args.clean` as the third positional argument to `run_test_in_subfolders`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.