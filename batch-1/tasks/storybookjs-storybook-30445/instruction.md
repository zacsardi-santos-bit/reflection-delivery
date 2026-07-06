Implement a shared state management system for Storybook to synchronize state across isolated environments using the existing channel infrastructure. Create a UniversalStore with a leader/follower model to manage state updates and event propagation. Ensure the system handles lifecycle states, error conditions, and provides a React hook for state subscription.

Requirements:

*   Implement the `UniversalStore` class in `code/core/src/shared/universal-store/index.ts`:
    *   Use `UniversalStore.create()` as the only method to instantiate a store. Direct constructor calls must throw a `TypeError`.
    *   `create()` must accept options: `id` (string, required), `leader` (boolean, required), `initialState` (optional), `debug` (optional).
    *   Ensure `create()` returns existing instances if the id already exists, logging a warning.
    *   Define store lifecycle states: 'UNPREPARED', 'SYNCING', 'READY', 'ERROR' using `UniversalStore.Status`.
    *   Implement `UniversalStore.__prepare(channel, environment)` to initialize the channel and transition store states.
    *   Detect and handle multiple leaders with the same id, logging an error and setting status to ERROR.
    *   Synchronize follower states with leaders, rejecting `untilReady()` if no leader is found.
    *   Implement state management methods: `getState()`, `setState()`, `onStateChange()`, `subscribe()`, and `send()`.
    *   Ensure `setState()` and `send()` throw errors if the store is not READY.
    *   Re-emit events from followers on leader's channel with forwarding actor info.
    *   Log debug information if `debug` is true.

*   Provide the `useUniversalStore` hook in `code/core/src/shared/universal-store/use-universal-store-manager.ts`:
    *   Accept a `UniversalStore` instance and an optional selector function.
    *   Return a `[state, setState]` tuple, re-rendering components on state changes.

*   Define `ChannelEvent` type in `code/core/src/shared/universal-store/types.ts` to represent channel messages.

*   Implement a manual mock for the `instances` registry in `code/core/src/shared/universal-store/__mocks__/instances.ts`:
    *   Use a spyable `set` method that stores instances.
    *   Implement `get`, `has`, and `clearAllEnvironments()` methods to manage instance storage and isolation by environment.

*   Ensure the real instances module exists at `code/core/src/shared/universal-store/instances.ts` and is used by `UniversalStore`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.