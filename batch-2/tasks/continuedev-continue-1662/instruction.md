Implement a standalone directory traversal utility and enhance the code chunking feature to handle structured code files more effectively. Ensure that the utility respects ignore file patterns and that the chunking process maintains semantic code units.

*   Implement the `codeChunker` function in `core/indexing/chunk/code.ts`.
    *   Accept `filename`, `contents`, and `maxChunkSize` as parameters.
    *   Return an async generator of chunks, each with a `content` field.
    *   Yield no chunks for empty string input.
    *   Yield a single chunk with the entire file content if it fits within `maxChunkSize`.
    *   For structured code files, yield each top-level class or function definition as a separate chunk.
    *   For large classes, yield a summary chunk with placeholder content for methods and separate chunks for each method.

*   Implement the `walkDir` function in `core/indexing/walkDir.ts`.
    *   Accept `dirPath`, `ide`, and an optional `WalkerOptions` object.
    *   Return a Promise resolving to a list of file paths.
    *   By default, return paths relative to `dirPath`; return absolute paths if `returnRelativePaths` is false.
    *   Return an empty array for an empty directory.
    *   Return filenames in sorted order for a flat directory.
    *   Respect `.gitignore` and `.continueignore` files, including negation, wildcard, root-anchored, and directory-level patterns.
    *   Apply ignore files only to their own directory and subdirectories.
    *   Include `.gitignore` and `.continueignore` files in results.
    *   Support `WalkerOptions.onlyDirs` and `WalkerOptions.includeEmpty` to return only directory paths.
    *   Handle complex combinations of patterns in ignore files.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.