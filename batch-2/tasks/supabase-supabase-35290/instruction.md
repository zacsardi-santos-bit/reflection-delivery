Implement a semantic search capability in the Supabase documentation site's GraphQL API. Add a new query field to enable clients to search documentation content using AI-generated embeddings. Ensure the search results include essential fields and handle errors gracefully.

*   Add a GraphQL query field named `searchDocs` to the RootQueryType in `apps/docs/resources/rootSchema.ts`.
    *   Accept a required `query` argument of type `String!`.
    *   Accept an optional `limit` argument of type `Int`.
    *   Return a `SearchResultCollection` type with a `nodes` array.

*   Ensure each node in the `nodes` array includes:
    *   A `title` field, mapped from the `page_title` field in the database RPC result.
    *   An `href` field.
    *   A `content` field, which is included only if explicitly requested in the GraphQL selection set.

*   Implement the `search_content` RPC call:
    *   Pass `include_full_content: true` when the `content` field is requested.
    *   Use the `max_result` parameter set to the `limit` value when provided.

*   Handle errors appropriately:
    *   Return a validation error containing the word 'required' if the `query` argument is omitted.
    *   Return an error with the message 'Internal Server Error' if embedding creation fails.

*   Export the `OpenAIClientInterface` from `apps/docs/lib/openAi.ts`:
    *   Declare a method `createContentEmbedding(text: string): Promise<Result<number[], ApiErrorGeneric>>`.

*   Ensure the route handler file is compatible with a test environment where `server-only` is mocked as an empty module.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.