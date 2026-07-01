I'm working on adding a relation loading strategy option to the Prisma query engine. Right now, when a query includes nested relations, there's no way to control whether related records are fetched using SQL joins or separate queries. I want to introduce a new optional argument that can be passed to top-level read and single-record write operations to choose between these two strategies.

The join-based strategy should use lateral joins and would only be available on databases that support them (like PostgreSQL and CockroachDB). The separate-query strategy should work on all supported databases. Importantly, both strategies must return the same data — they're just different execution approaches.

The argument should only be accepted at the top level of a query or mutation. Nested relation fields, aggregation operations, group-by operations, and bulk mutation operations (batch create, update, or delete) should not accept this argument and should return a validation error if someone tries to use it there.

Additionally, the query validation layer needs to be updated to recognize this new argument so that when it reports an error about an unknown argument, it correctly includes the new argument in the list of valid alternatives it suggests.
