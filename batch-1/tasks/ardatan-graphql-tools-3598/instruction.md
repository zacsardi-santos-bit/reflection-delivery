Implement a function to resolve a GraphQL schema using a master schema file that contains only import directives. Ensure all transitively required types across multiple levels of imports are included in the final schema output.

*   Implement the `importSchema` function in `packages/import/src/index.ts`.
    *   Signature: `importSchema(schema: string, schemas?: Record<string, string>, options?: ImportSchemaOptions) -> string`.
    *   The function must resolve a GraphQL schema file using `# import` directives, recursively collecting and merging all imported type definitions into a single SDL string.
    *   Ensure it correctly handles cases where the entry file contains only import directives and no type definitions (master schema pattern).

*   Ensure the function correctly resolves all transitively needed types across three or more levels of nesting.
    *   Handle mixed import styles: specific named types at some levels and wildcard imports at others.
    *   The resolved schema must include all reachable type definitions in the SDL output.

*   Verify the function with the provided fixture files:
    *   `level1.graphql` (master schema): imports `Query.*` from `./level2.graphql`, contains no type definitions.
    *   `level2.graphql`: imports `Account` from `./level3.graphql`, defines `User`, `PaginatedWrapper`, `Query`.
    *   `level3.graphql`: imports `*` from `./level4.graphql`, defines `Cart`, `Account`.
    *   `level4.graphql`: defines `Product`, `Products`.

*   Ensure the resolved schema includes the following types: `Account`, `Cart`, `PaginatedWrapper`, `Product`, `Products`, `Query`, and `User`.
*   The resolved SDL must be a valid GraphQL schema document containing each required type definition with its correct fields as defined in the source fixture files.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.