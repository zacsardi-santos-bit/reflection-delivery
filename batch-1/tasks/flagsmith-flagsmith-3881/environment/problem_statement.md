## Description

The edge V2 migration process that moves identity overrides to the new DynamoDB format currently runs without any controls on how much DynamoDB read capacity it consumes. This means a migration can run indefinitely, consuming unlimited read capacity and potentially causing performance or cost issues in production environments.

We need to add a read capacity budget mechanism to the migration so that operators can throttle it. When the migration exhausts the configured budget, it should stop gracefully and record itself as incomplete rather than failing with an error, allowing it to be retried or resumed in a future run.

## Expected Behavior

- A configurable read capacity budget can be set per project, with a system-wide fallback setting as the default.
- The migration process respects the budget: it tracks DynamoDB read capacity consumed across paginated queries and stops when the budget is reached.
- When the budget is exhausted mid-migration, the migration status is recorded as incomplete (a new distinct status, separate from "in progress" or "not started").
- A project with an incomplete migration status does not show edge identity overrides for features (same behavior as not-started and in-progress states).
- The migration status field on the project model has been renamed to better reflect its broader scope.

## Why This Matters

Without capacity controls, running the edge V2 migration on large datasets risks unbounded DynamoDB read consumption. The ability to set a budget and resume partial migrations allows operators to safely run the migration in a controlled, incremental way.
