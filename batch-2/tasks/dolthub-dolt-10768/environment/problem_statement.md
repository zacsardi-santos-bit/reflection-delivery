## Description

In a Dolt cluster with primary and standby roles, data from the primary is pushed to standby nodes through a remote API endpoint during replication. When writes arrive at a standby this way, all registered commit hooks fire — including hooks that are only meaningful on the primary (such as outgoing replication hooks). This causes spurious warning messages about receiving a commit callback while not being the primary role to appear in the standby's server logs during every normal replication event.

## Expected Behavior

- Commit hooks should be able to declare whether they want to fire when a write arrives through the replica (standby) path.
- Hooks that only make sense on the primary should be able to opt out of replica write execution, so they don't fire and don't produce noisy warnings on standbys.
- Hooks relevant to maintenance operations (stats, GC) should be able to opt in.
- Standby server logs should not contain spurious warnings about processing commit callbacks while not in the primary role, during normal cluster replication.

## Infrastructure

Additionally, the integration test server driver should support asserting that specific patterns do NOT appear in server log output, complementing the existing ability to assert that patterns DO appear. This allows tests to verify that unwanted log messages have been suppressed.

## Why This Matters

Without this fix, standby server logs are polluted with warning messages during routine replication, making it harder to detect genuine problems. Hooks that should be no-ops on standbys can also trigger unintended side effects, such as replication loops.
