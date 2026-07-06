## Description

Several hooks in the CLI's UI layer read configuration settings from a shared React context, creating tight coupling that makes these hooks difficult to reuse and test in isolation. To test these hooks, tests must set up a full context provider wrapper just to supply settings data — adding unnecessary complexity.

## Expected Behavior

- The hook responsible for managing editor preferences should accept the settings object as a direct parameter rather than reading it from a global React context.
- Tests for this hook should be able to pass a mock settings object directly when calling the hook, without needing to wrap it in a context provider.
- The hook should continue to support all existing behaviors: managing dialog open/close state, selecting and persisting editor preferences, clearing preferences, handling unavailable or sandbox-restricted editors, and propagating errors.

## Why This Matters

Hooks with implicit context dependencies are harder to test, document, and reuse. Making the settings dependency explicit as a parameter reduces coupling and makes the hook independently testable — callers provide the settings directly rather than relying on a global provider being in scope.
