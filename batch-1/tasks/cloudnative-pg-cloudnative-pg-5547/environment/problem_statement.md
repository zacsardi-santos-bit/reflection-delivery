## Description

The managed database controller currently embeds all SQL operations (creating, modifying, and dropping PostgreSQL databases) directly inside controller methods. This makes the logic hard to unit test in isolation since it is tightly coupled to the reconciler. In addition, when a database SQL operation fails, the error propagates up through the reconciliation loop rather than being captured in the managed object's status fields.

## Expected Behavior

- The PostgreSQL SQL operations for detecting, creating, updating, and dropping managed databases should be extracted into standalone, independently testable functions.
- When a database operation fails during reconciliation, the error should be reflected on the managed database object's status: the object should be marked as not ready and the error message should be stored in the status error field. The reconciliation loop itself should not return an error in this case.
- There should be explicit methods for marking a reconciliation as succeeded (marking the database as ready with no error) or failed (marking the database as not ready with an error message).
- The reconciler's internal instance dependency should be expressed as an interface so that tests can substitute a fake implementation without requiring a real PostgreSQL instance.

## Why This Matters

Without this refactoring, it is not possible to write unit tests for the individual SQL operations or for the status update logic without spinning up a real Kubernetes and PostgreSQL environment. Surfacing errors in the managed object's status improves observability — operators can see why a database is not ready without inspecting controller logs.
