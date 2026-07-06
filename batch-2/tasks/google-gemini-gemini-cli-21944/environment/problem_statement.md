## Description

The core execution pipeline passes configuration context to the scheduler, tool executor, and policy components as a mix of separate arguments — a configuration object here, a message bus there, with the tool registry accessed via a method call and the current prompt identifier retrieved from an indirect storage mechanism. This fragmentation makes the interfaces inconsistent, verbose, and harder to maintain.

## Expected Behavior

- A single unified context interface should consolidate configuration, tool registry, message bus, and prompt identity into one object.
- The scheduler should accept this unified context as a named field (replacing the previously named config field).
- The tool executor should accept this unified context as a single argument instead of multiple separate ones.
- The policy update function should accept this unified context directly, rather than receiving a separate config-and-message-bus wrapper object.
- The tool registry should be accessible as a direct property on the context rather than through a method call.
- The current prompt identifier should be accessible as a direct property on the context rather than retrieved from an indirect storage mechanism.
- Sub-agent schedulers should receive the appropriate agent-specific context, including the overridden tool registry, through this unified interface.

## Why This Matters

This change makes it easier to understand what dependencies each component requires, reduces the number of separate arguments that must be passed together, and ensures consistent access patterns across the codebase. It also makes the current prompt identifier and tool registry available uniformly without relying on separate method calls or async local storage.
