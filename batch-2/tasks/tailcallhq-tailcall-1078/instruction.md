Implement a caching mechanism by creating a common interface that computes a unique numeric identifier for requests across HTTP, GraphQL, and gRPC protocols. Ensure that this identifier, a 64-bit unsigned integer, is derived from request parameters such as URL, headers, and body content, and that it is collision-resistant.

*   Create a trait named `CacheKey` in the `src/lambda/cache.rs` file.
    *   Make it generic over a context type parameter `Ctx`.
    *   Include the method signature: `fn cache_key(&self, ctx: &Ctx) -> u64`.
    *   Re-export this trait publicly as `crate::lambda::CacheKey`.

*   Implement `CacheKey` for the HTTP request template in `src/http/request_template.rs`.
    *   Ensure it works for context types that provide path string resolution and header access.
    *   Compute the cache key by hashing the rendered URL, all header names and values, and the rendered body.
    *   Ensure different URLs, headers, or body contents produce distinct keys.
    *   Provide a method `with_body` to set a body template:
        *   Signature: `pub fn with_body(self, body: Mustache) -> Self`.

*   Implement `CacheKey` for the GraphQL request template in `src/graphql/request_template.rs`.
    *   Ensure it works for context types that provide path GraphQL resolution, header access, and GraphQL operation context.
    *   Compute the cache key by hashing the fully rendered GraphQL query string.
    *   Ensure different argument values or operation parameters produce distinct keys.

*   Implement `CacheKey` for the gRPC request template in `src/grpc/request_template.rs`.
    *   Ensure it works for context types that provide path string resolution and header access.
    *   Compute the cache key by rendering the request and hashing the rendered URL and body.
    *   Ensure different rendered bodies or URLs produce distinct keys.
    *   Ensure the rendered request struct implements `Hash` over its `url` and `body` fields.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.