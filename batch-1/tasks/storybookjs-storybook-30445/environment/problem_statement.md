## Description

Storybook runs in multiple isolated environments — the manager (the sidebar/UI shell) and one or more preview frames (where stories render). There is currently no standardized mechanism for sharing and synchronizing state across these boundaries. As a result, any feature that needs consistent data on both sides must implement its own bespoke channel-based communication, which is error-prone and repetitive.

We need a universal, reusable store that can synchronize state between environments using the existing channel infrastructure.

## Expected Behavior

- Developers can create a named store in any environment; one instance acts as the leader (authoritative state owner) and others act as followers (synchronized copies).
- When a leader is created, followers in other environments automatically discover it and receive the current state.
- Any participant (leader or follower) can update the state; changes propagate to all other participants.
- Custom events can also be sent through the store and are delivered to all subscribers across environments.
- The store has a well-defined lifecycle (not ready, syncing, ready, error) that developers can inspect and await before performing operations.
- Attempting to operate on the store before it is ready must produce a descriptive error.
- If multiple leaders are accidentally created for the same store id, both should be flagged with an error status and a clear diagnostic message should be logged.
- A hook should be available for manager-side components to subscribe to a store's state, with optional selector support to avoid unnecessary re-renders.

## Why This Matters

Without this, every cross-environment feature requires custom plumbing. A universal store reduces boilerplate, enforces consistent patterns, and makes it easier to build reliable features that span the manager and preview.
