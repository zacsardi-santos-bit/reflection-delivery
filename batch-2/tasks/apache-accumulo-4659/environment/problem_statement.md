## Description

Accumulo server processes (manager, tablet server, etc.) always create their own internal data context at startup, with no way to substitute a different implementation. This makes it impossible to write integration tests that simulate real-world failure modes — particularly the case where a conditional metadata write returns an ambiguous result (the operation may or may not have been applied, but the caller cannot tell which).

When a conditional write reports an "unknown" outcome, specific recovery callbacks are supposed to fire and handle the uncertainty gracefully. However, because the context cannot be swapped out during testing, these callbacks rarely (if ever) get exercised by the automated test suite, leaving a significant code path untested.

## Expected Behavior

- Server processes should accept a factory that produces the data context at startup, so tests and subclasses can inject alternative implementations.
- A test-only server context implementation should exist that intercepts conditional writes and randomly returns an ambiguous ("unknown") result — simulating real-world network or storage unreliability.
- The interceptor for conditional writes should have a simpler interface: instead of separate "before-write" and "after-write" hooks, implementations should take full control by receiving the underlying writer and deciding how and whether to forward the mutations.
- The comprehensive integration test suite should have a variant that runs the full test suite under these flaky conditions, verifying that all the existing API paths handle ambiguous mutation results correctly without data loss or corruption.

## Why This Matters

The recovery logic that fires when a conditional mutation's outcome is unknown is critical for correctness under real network conditions. Without the ability to inject a flaky context, this logic goes untested, and bugs in the rejection handlers can go undetected until production.
