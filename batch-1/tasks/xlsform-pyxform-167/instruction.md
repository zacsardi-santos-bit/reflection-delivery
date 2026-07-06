Implement support for a second validator in the XLSForm conversion tool, allowing users to toggle between validators via command-line flags. Fix bugs in the error-cleaning utility to ensure single-line errors are preserved and file path errors are returned correctly. Move the error-cleaning logic to a dedicated module for better organization.

*   Implement the `ErrorCleaner` class in `pyxform/validators/error_cleaner.py`:
    *   Provide a static method `odk_validate(error_message: str) -> str` to clean and format ODK validator error output.
    *   Ensure `odk_validate` handles multi-line XPath and Javarosa error messages, removing Java stack traces, deduplicating, and tokenizing XPath node references.
    *   Preserve single-line error messages in the output.
    *   Return error messages containing 'Error: Unable to access jarfile' unchanged.

*   Update the `pyxform/validators/enketo_validate/` package:
    *   Implement `check_xform(path_to_xform: str) -> list` to run Enketo validation on an XForm path, returning warning strings.
    *   Implement `_node_installed() -> bool` to check if node.js is installed, returning True if available.

*   Modify `pyxform/xls2xform.py`:
    *   Update `_create_parser()` to accept:
        *   `--skip_validate` flag (action='store_false', default=True).
        *   `--enketo_validate` flag (action='store_true', default=False).
        *   `--odk_validate` flag (action='store_true', default=False).
    *   Implement `_validator_args_logic(args: argparse.Namespace) -> argparse.Namespace` to determine active validators:
        *   Disable both validators if `--skip_validate` is passed.
        *   Enable only ODK validator by default if no flags are given.
        *   Enable only Enketo validator if `--enketo_validate` is passed.
        *   Enable only ODK validator if `--odk_validate` is passed.
        *   Enable both validators if both `--odk_validate` and `--enketo_validate` are passed without `--skip_validate`.

*   Ensure the `strings.ini` fixture file for validator utility tests includes the section `[TestValidatorUtil]` with specific test entries.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.