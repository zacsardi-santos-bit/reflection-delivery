Implement a new optional argument to control how related records are loaded in the Prisma query engine. This argument should allow developers to choose between using SQL joins or separate queries for loading related data. Ensure this option is available only on specific operations and handle validation errors appropriately.

*   Define a new enum type `RelationLoadStrategy` in the query engine schema with two values:
    *   `join` - for loading relations using SQL lateral joins (supported on PostgreSQL and CockroachDB).
    *   `query` - for loading relations using separate queries (supported on all databases).

*   Add a new argument `relationLoadStrategy` of type `RelationLoadStrategy` to the following operations:
    *   Top-level single-record read operations: `findMany`, `findFirst`, `findFirstOrThrow`, `findUnique`, `findUniqueOrThrow`.
    *   Top-level single-record write operations: `createOne`, `updateOne`, `deleteOne`, `upsertOne`.

*   Ensure the query validation error output includes `relationLoadStrategy` in the list of valid arguments with typeNames containing `RelationLoadStrategy`.

*   Implement the following behavior based on the `relationLoadStrategy` value:
    *   Use `LEFT JOIN LATERAL` SQL syntax when `relationLoadStrategy: join` is specified on PostgreSQL or CockroachDB.
    *   Use separate queries for relation loading when `relationLoadStrategy: query` is specified, applicable to all supported connectors.

*   Ensure both `join` and `query` strategies return identical response data for the same query and dataset.

*   Handle validation errors for incorrect usage of `relationLoadStrategy`:
    *   Return error code 2009 with the message `Argument does not exist in enclosing type` when used on nested relation fields.
    *   Return error code 2009 with the message `Argument does not exist in enclosing type` when used on aggregate operations, groupBy operations, or bulk mutation operations.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.