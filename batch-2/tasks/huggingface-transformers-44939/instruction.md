Refactor the modeling structure linter into a proper Python package and implement three new lint rules. Update the Continuous Integration (CI) test routing logic to ensure the linter's tests are automatically included when relevant files change.

*   Reorganize the linter into a Python package:
    *   Place the package under `utils/mlinter/` with submodules `mlinter.py`, `trf011.py`, and an `__init__.py`.
    *   Ensure the package is importable using `from utils.mlinter import mlinter` and `from utils.mlinter import trf011`.

*   Implement the `mlinter` module:
    *   Define `analyze_file(file_path: Path, source: str, enabled_rules: set = None) -> list` to analyze Python source code for violations, returning a list of violation objects with `.rule_id` and `.message` attributes.
    *   Define `get_changed_modeling_files(base_branch: str) -> dict` to return changed modeling files, using `subprocess` for internal operations.
    *   Expose rule constants `TRF001` through `TRF013` as module-level attributes.

*   Implement new lint rules:
    *   Rule `TRF008`: Flag `@add_start_docstrings` used with empty or no string arguments, producing a message containing 'without non-empty docstring arguments'.
    *   Rule `TRF012`: Flag in-place operations on module weight attributes inside `_init_weights` methods, producing a message containing 'in-place operation on a module\'s weight'.
    *   Rule `TRF013`: Flag `__init__` methods in non-PreTrainedModel classes that don't call `self.post_init()`, producing a message containing 'does not call `self.post_init`'.

*   Implement the `trf011` module:
    *   Expose `_PP_PLAN_MODULES_BY_MODEL_DIR` as a module-level dictionary for independent patching in tests.

*   Update the `tests_fetcher` module:
    *   Define `get_repo_utils_tests() -> list` to return test paths including 'tests/repo_utils/test_mlinter.py' and 'tests/repo_utils/test_tests_fetcher.py'.
    *   Define `should_run_repo_utils_tests(changed_files: list) -> bool` to return `True` if any file under `utils/mlinter/` changes.
    *   Define `create_test_list_from_filter(test_list: list, out_path: str) -> None` to write repo-utils test paths to 'tests_repo_utils_test_list.txt' inside `out_path`.
    *   Update `infer_tests_to_run` to include 'tests/repo_utils/test_mlinter.py' when 'utils/mlinter/mlinter.py' is modified.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.