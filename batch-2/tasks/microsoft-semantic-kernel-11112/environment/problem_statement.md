## Description

The Postgres connector includes a SQL generation helper class that builds all the SQL commands needed to interact with the database (creating tables, indexes, querying, upserting, deleting records, etc.). Currently, this helper is implemented as an instantiable class that also implements a separate interface. However, none of its methods use any instance state — they are all purely functional utilities that take inputs and produce SQL commands.

This design forces callers to create an object solely to call stateless functions, and it requires maintaining an interface that provides no real benefit for a stateless utility. The result is extra complexity that serves no purpose.

## Expected Behavior

- The SQL generation helper should be refactored into a static class with static methods, removing the need to instantiate it.
- The associated interface that the class previously implemented should be removed.
- All internal call sites that previously created an instance of the helper and called its methods should be updated to call the static methods directly.
- All existing Postgres connector unit tests (property mapping, record mapping, collection operations, DI registration, vector store behavior) must continue to pass.

## Why This Matters

Keeping a stateless utility as an instance class with an interface adds maintenance burden without benefit. Making it static better reflects the nature of the code — pure SQL generation functions with no side effects — and simplifies the connector's internal design.
