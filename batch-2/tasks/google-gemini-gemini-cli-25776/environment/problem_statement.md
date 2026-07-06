## Description

When the CLI is communicating with a remote agent, that agent can send intermediate status messages during processing to signal its progress and thoughts. These messages are currently being collected internally but are never surfaced individually to the user as distinct activity entries. As a result, users see only a generic "working" placeholder regardless of how many status messages the agent has sent.

Additionally, the mechanism for reloading the agent registry — which is responsible for re-registering or unregistering agents when the configuration changes — is only reachable as a private implementation detail. This makes it impossible to trigger cleanly from outside the class. There is also a bug in the reload flow: when the reload is triggered, the registry ends up being initialized twice (once during the reload itself and once in the post-reload callback), which corrupts the registered agent state. This causes agents that were disabled and then re-enabled to not appear correctly after a reload.

## Expected Behavior

- Each status message received from a remote agent during a session should be returned as a separate, completed activity item when the current activity list is queried.
- The agent registry should expose a public reload interface so that callers can trigger re-registration without accessing private internals.
- After triggering a reload, agents that were disabled should be unregistered and agents that were re-enabled should be properly registered, with no double-initialization side effects.

## Why This Matters

Users should be able to see the intermediate messages an agent sends while it works, rather than a static spinner. Hiding the reload mechanism as a private detail also makes the agent registry difficult to use correctly from external code and tests, and the double-initialization bug silently prevents re-enabled agents from becoming available after a configuration change.
