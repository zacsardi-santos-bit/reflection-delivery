Implement validation for keyboard shortcut keys in the `UserConfig` class to ensure they follow the required format. Ensure that each shortcut key contains exactly one forward slash to separate the context from the action name. Raise an error if the format is incorrect during configuration initialization.

*   Update the `UserConfig` class located in `spyder/config/user.py`.
    *   Validate shortcut keys in the `__init__` method.
    *   Use the constructor parameters: `name`, `path`, `defaults`, `load`, `version`, `backup`, and `raw_mode`.
*   Ensure that the 'shortcuts' section in the defaults list is validated:
    *   Each shortcut key must contain exactly one forward slash ('/').
    *   Raise a `ValueError` if a shortcut key contains no forward slash (e.g., 'foo').
    *   Raise a `ValueError` if a shortcut key contains more than one forward slash (e.g., 'editor/foo/bar').

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.