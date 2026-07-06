Create a Pants plugin to automate the injection of common metadata into StackStorm's Python distributions during wheel builds. This plugin should centralize metadata management, reducing duplication and potential inconsistencies across `python_distribution()` targets.

*   Implement the `StackStormSetupKwargsRequest` class:
    *   Ensure it subclasses `SetupKwargsRequest` and is applicable to all targets by implementing the `is_applicable` class method to return `True`.

*   Implement the `setup_kwargs_plugin` function:
    *   Require 'description' and 'version_file' kwargs in the `provides` field.
        *   Raise `ValueError` with 'Missing a `description` kwarg in the `provides` field' if 'description' is absent.
        *   Raise `ValueError` with 'Missing a `version_file` kwarg in the `provides` field' if 'version_file' is absent.
    *   Extract the version from the specified `version_file` by parsing the `__version__` variable.
        *   Raise an error with 'Unmatched glob from StackStorm version file' if the file does not exist.
        *   Raise `ValueError` with 'Could not find the __version__' if the variable is not found.
    *   Automatically set:
        *   `author` to "StackStorm" and `author_email` to "info@stackstorm.com".
        *   `url` to "https://stackstorm.com" and `license` to "Apache License, Version 2.0".
        *   `project_urls` with keys 'Pack Exchange', 'Repository', 'Documentation', 'Community', 'Questions', 'Donate', 'News/Blog', 'Security', and 'Bug Reports'.
    *   Raise `ValueError` with 'These kwargs should not be set in the `provides` field' if any of `version`, `author`, `license`, `project_urls`, or `long_description` kwargs are manually provided.
    *   Use `README.rst` as `long_description` and set `long_description_content_type` to 'text/x-rst' if it exists.
    *   Prepend `META_CLASSIFIERS` and `LINUX_CLASSIFIER` to the classifiers list, preserving user-provided classifiers.
    *   Add Python programming language classifiers: 'Programming Language :: Python', 'Programming Language :: Python :: 3', 'Programming Language :: Python :: 3.6', and 'Programming Language :: Python :: 3.8'.

*   Define constants in `pants-plugins/release/rules.py`:
    *   `REQUIRED_KWARGS`: Tuple containing "description" and "version_file".
    *   `PROJECT_METADATA`: Dictionary with common metadata values.
    *   `PROJECT_URLS`: Dictionary with project URLs.
    *   `META_CLASSIFIERS`: Tuple with standard classifiers.
    *   `LINUX_CLASSIFIER`: "Operating System :: POSIX :: Linux".

*   Implement `python_classifiers` function to generate Python language classifiers for specified versions.

*   Implement `rules` function to return the list of Pants rules to register, including `collect_rules()` and the `UnionRule` for `StackStormSetupKwargsRequest`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.