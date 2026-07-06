Implement improvements to the GraphQL language server for better handling of missing configuration files and separation of document parsing logic. Ensure the server provides clear feedback when a configuration file is missing and refactor the document parsing logic into a dedicated module.

*   Implement the `parseDocument` function in `packages/graphql-language-service-server/src/parseDocument.ts`:
    *   Accept `text` (string) and `filePath` (string) as arguments.
    *   Return an array of objects, each with a `query` property (string).
    *   Extract GraphQL queries from:
        *   Tagged template literals using `gql` in `.js`, `.ts`, and `.tsx` files.
        *   Call expressions with `gql` and template literals.
        *   Templates with a `#graphql` prefix comment or `/* GraphQL */` block comment.
    *   Replace interpolated expressions in queries with empty strings.
    *   Return an empty array for:
        *   Empty files, whitespace-only files, unsupported extensions (e.g., `.txt`).
        *   Files that cannot be parsed without throwing exceptions.

*   Update the `MessageProcessor` class in `packages/graphql-language-service-server/src/MessageProcessor.ts`:
    *   Expose `_isInitialized` and `_isGraphQLConfigMissing` as public instance properties.
    *   Set `_isInitialized` to `false` and `_isGraphQLConfigMissing` to `true` when no valid GraphQL config file exists.
    *   Call `logger.error` once with the message: "GraphQL Config file is not available in the provided config directory" when the config file is missing or empty.

*   Ensure the `findGraphQLTags` function:
    *   Returns an empty array for JavaScript source content that produces no usable AST nodes (e.g., files containing only comments) without crashing.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.