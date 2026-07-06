Implement a utility function and a constant in the `filesys` package of the kustomize project to enhance path manipulation capabilities. The function should remove trailing path separators from a given string, and the constant should represent the current directory notation.

*   Export a function `StripTrailingSeps(s string) string` in the `filesys` package.
    *   Ensure it removes all trailing filepath separators from the input string.
    *   Handle specific cases:
        *   Return the input unchanged if there are no trailing separators.
        *   Return an empty string if the input is an empty string or consists entirely of separators.
        *   Examples:
            *   `StripTrailingSeps("foo")` should return `"foo"`.
            *   `StripTrailingSeps("")` should return `""`.
            *   `StripTrailingSeps("foo/")` should return `"foo"`.
            *   `StripTrailingSeps("foo///bar///")` should return `"foo///bar"`.
            *   `StripTrailingSeps("/////")` should return `""`.
            *   `StripTrailingSeps("/")` should return `""`.
*   Export a constant `DotDir` in the `filesys` package.
    *   Set its value to the string `"."`.
    *   Ensure it represents the current directory notation correctly.
*   Ensure the `api/filesys` package compiles successfully and all existing tests continue to pass.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.