Update the Remote Config SDK's condition evaluator to use a modern hashing library compatible with current Node.js versions. Ensure that hash values are correctly processed as native 64-bit integers and maintain existing targeting behavior.

*   Replace the hashing library in `src/remote-config/condition-evaluator-internal.ts`:
    *   Import the `farmhash-modern` package instead of `farmhash`.
    *   Use the `fingerprint64` function from `farmhash-modern`, which returns a `bigint`.
    *   Convert the `bigint` to a string using `.toString()` before passing it to `long.fromString()` for numeric processing.

*   Update the project's `package.json`:
    *   Remove the `farmhash` dependency.
    *   Add `farmhash-modern` with version `^1.1.0` as a production dependency.

*   Ensure compatibility with existing percent-condition evaluations:
    *   Maintain correct behavior for LESS_OR_EQUAL, GREATER_THAN, and BETWEEN operators using the new library.

*   Adjust test behavior for unsupported Node.js versions:
    *   Skip tests that use the actual hash function on Node.js version 14 by checking `process.versions.node.startsWith('14')`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.