Enable strict compiler warning settings for the litehtml project, ensuring all warnings are treated as errors. Resolve all identified C++ issues in the codebase and test infrastructure to ensure the project compiles cleanly. Update URL test data to fully specify all components.

*   Configure the build system to treat all compiler warnings as errors.
    *   For non-MSVC compilers, enable extra warnings and pedantic mode.
    *   For MSVC, enable a high warning level.

*   Resolve signed/unsigned integer comparison warnings.
    *   Apply explicit casts to signed integer types when comparing signed integers against unsigned values.

*   Use idiomatic string empty-check patterns.
    *   Replace comparisons against empty string literals with the `.empty()` method.

*   Correct string search operation comparisons.
    *   Compare the return value of `string::find` with `string::npos` instead of `-1`.

*   Fully initialize aggregate struct or class initializers.
    *   Ensure all fields are initialized in brace-initializers.

*   Use the null pointer literal for null pointer values.
    *   Replace integer `0` with `nullptr` in returned expressions.

*   Suppress unused function parameters.
    *   Use comment-wrapping syntax (e.g., `/* param_name */`) for unused parameters.

*   Update specific files with necessary corrections:
    *   `containers/test/Font.cpp`
        *   Fully initialize the terminal entry of the `installed_fonts` array with both integer and string fields.
        *   Fix signed/unsigned comparisons.
    *   `containers/test/test_container.cpp`
        *   Apply unused-parameter suppression.
        *   Use `auto` for cast locals.
        *   Replace integer-zero null pointer returns with `nullptr`.
    *   `containers/test/test_container.h`
        *   Apply unused-parameter suppression in all override method declarations.
        *   Replace integer-zero null pointer return with `nullptr`.
    *   `test/url_test.cpp`
        *   Update URL test case entries to specify all five components (scheme, host, path, query, fragment), using empty strings for missing query and fragment fields.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.