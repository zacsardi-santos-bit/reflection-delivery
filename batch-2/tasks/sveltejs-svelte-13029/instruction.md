Update the Svelte test suite to align with the behavior of upgraded testing dependencies. Modify tests to accommodate changes in CSS color normalization and source map field handling.

*   Ensure computed color style assertions in tests use normalized numeric RGB notation:
    *   Convert named colors to their RGB equivalents: 
        *   'red' to 'rgb(255, 0, 0)'
        *   'green' to 'rgb(0, 128, 0)'
        *   'blue' to 'rgb(0, 0, 255)'
        *   'black' to 'rgb(0, 0, 0)'
        *   'purple' to 'rgb(128, 0, 128)'
        *   'pink' to 'rgb(255, 192, 203)'
*   Ensure transparent background colors return 'rgba(0, 0, 0, 0)' instead of an empty string when queried.
*   Update source map comparison logic in preprocessor tests:
    *   Strip the 'ignoreList' field from source map objects before comparison to prevent false test failures due to its presence.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.