Reorganize the Tact compiler's standard library access by centralizing the stdlib content and path information into dedicated modules. Ensure that the compiler continues to function correctly, specifically for contract compilation and memory allocation resolution, using the new stdlib locations.

*   Create a module at `src/stdlib/stdlib.ts`:
    *   Export a default value of type `Record<string, string>`.
    *   Ensure each key is a stdlib file path relative to the stdlib root (e.g., "std/primitives.tact").
    *   Ensure each value is the base64-encoded content of the corresponding file.
    *   Include all necessary stdlib `.tact` and `.fc` files previously bundled at `src/imports/stdlib.ts`.

*   Create a module at `src/stdlib/path.ts`:
    *   Export a named constant `stdlibPath` of type `string`.
    *   Ensure `stdlibPath` represents the absolute path to the stdlib root directory.
    *   Verify that `path.join(stdlibPath, "/std/primitives.tact")` resolves to an existing readable file.

*   Ensure the `src/imports/stdlib.ts` file is no longer required by the tests, and that `src/stdlib/stdlib.ts` serves as the canonical source for stdlib content.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.