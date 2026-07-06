Update the boolean values in the embedded usage specification file to comply with the new KDL document language specification. Ensure that all boolean values are prefixed with a hash symbol to maintain compatibility with the updated version.

*   Modify the file located at `src/assets/mise-extra.usage.kdl`:
    *   Ensure the file contains at least one occurrence of the string `#true`.
    *   Convert all boolean values:
        *   Change any instance of the bare keyword `true` to `#true`.
        *   Change any instance of the bare keyword `false` to `#false`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.