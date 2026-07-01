## Description

The Prisma query engine needs a way for developers to control how related records are loaded during query execution. Currently, there is no way to choose between loading related data via SQL joins versus separate follow-up queries. Adding this flexibility would allow developers to pick the approach best suited to their use case and database.

## Expected Behavior

- A new optional parameter should be accepted on top-level read operations (find many, find first, find unique, and their "or throw" variants) and single-record write operations (create, update, delete, upsert).
- The parameter should accept two values: one indicating that related data should be loaded using SQL joins (available on PostgreSQL and CockroachDB), and another indicating separate queries should be used (available on all databases).
- Both approaches must return identical data — the choice is purely about execution strategy, not about the shape or content of the result.
- The parameter must not be accepted on nested relation fields, aggregation queries, group-by queries, or bulk mutation operations. Attempts to use it in those locations should be rejected with an appropriate validation error.
- The query validation schema must be aware of this new parameter so that error messages correctly list it among the valid arguments when an unknown argument is reported.

## Why This Matters

Some databases support more efficient ways to load related data in a single round-trip. Exposing this as a user-controlled option allows performance-conscious developers to take advantage of join-based loading on supported databases while still having a portable fallback strategy that works everywhere.
