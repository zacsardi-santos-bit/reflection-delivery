## Description

Streamlit's script run context currently holds several pieces of mutable per-run state — including widget ID tracking, form tracking, fragment tracking, and command telemetry — as top-level fields directly on the context object. This scattering of related state makes it harder to reset all of it atomically, reason about thread-safety boundaries, and understand at a glance what state is "shared" across threads within a single run.

This proposal groups all of that shared mutable state into a dedicated container object that lives inside the context. The context would expose this container as a single attribute, and all code that needs to read or write widget/form/fragment registrations or telemetry would go through that container.

## Expected Behavior

- A new dedicated container class exists to hold per-run shared state: widget ID sets, form ID sets, fragment ID sets, and command telemetry.
- The container is accessible on the context object as a single attribute.
- Each context instance owns its own independent container (not shared between context instances).
- Resetting the context also resets the container, clearing all tracked state in one step.
- The command telemetry stored in the container is thread-safe: concurrent writes from multiple threads do not lose any entries.
- The command telemetry exposes an immutable snapshot (not a mutable list), a count of stored commands, and a per-name raw count.
- Calling reset on the container clears all sets and resets the telemetry to empty state.

## Why This Matters

This refactor makes the shared-state boundary within a script run explicit and self-contained, which is important for correctness in multi-threaded execution (e.g., parallel fragments). It also simplifies cleanup: instead of resetting multiple independent fields, a single reset operation covers all of them.
