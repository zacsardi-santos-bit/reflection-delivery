I'm working on Qdrant's vector search system and I've run into a few related gaps in the general-purpose query endpoint.

First, the query endpoint doesn't support looking up reference vectors from a separate collection during recommendation queries. This feature already works in the dedicated recommendation endpoint — you can point it at a different collection to use as the source of example vectors — but when I try the same thing through the general query API, it doesn't work. This should work at both the top level of a query and inside nested prefetch queries, and the results should match what the dedicated endpoint produces.

Second, there's a security hole: when using JWT-based access control, the system doesn't check permissions on the collection you're looking up vectors from. A user who only has access to one collection can reference any other collection for vector lookups without being rejected. The system should return a permission error when a token doesn't grant access to the lookup collection — this needs to work both at the top-level query and inside prefetch queries.

Third, there's a missing validation: if someone provides both a fusion-style query and an explicit vector name selector in the same request, the system should reject it with a clear error rather than silently accepting an invalid combination.

All three of these — cross-collection lookup support in the query endpoint, access control enforcement on lookup collections, and the invalid combination validation — need to be in place with appropriate error messages for missing collections, missing points, and missing vector names.
