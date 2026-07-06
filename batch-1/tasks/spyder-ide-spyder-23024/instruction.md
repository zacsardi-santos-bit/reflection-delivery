Implement validation for shortcut key names in the `UserConfig` class to ensure they follow the required format. Raise an error if any key is malformed during initialization.

*   Update the `UserConfig` class located in `spyder/config/user.py` to validate shortcut keys.
    *   Ensure validation occurs in the `_check_defaults` method, which is called during the `__init__` method.
*   Validate that all shortcut option keys in the 'shortcuts' section of the provided defaults follow the format 'context/name'.
    *   The key must contain exactly one forward slash.
*   Raise a `ValueError` if:
    *   A shortcut option key contains no forward slash (e.g., 'foo').
    *   A shortcut option key contains more than one forward slash (e.g., 'editor/foo/bar').
*   Ensure validation is performed during object construction so that errors are raised immediately when invalid shortcut defaults are provided.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.