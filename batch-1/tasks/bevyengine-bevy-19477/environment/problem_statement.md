## Description

When initializing a system in Bevy's ECS framework, the initialization step currently returns nothing. This makes it impossible to retrieve the system's component access information directly from the initialization call.

Developers who want to inspect what components a system reads from or writes to — for example, to detect conflicts between two systems — currently cannot get that information back from initialization. They must rely on indirect mechanisms that are more cumbersome to use.

## Expected Behavior

- Calling the system initialization method should return the component access set for that system.
- The returned access set should allow the caller to immediately check for access conflicts with other access sets.
- Any system created from a regular function (using the standard system conversion mechanism) should implement this behavior.

## Why This Matters

This change makes it easier to build tooling, diagnostics, and analysis code that needs to work with system access patterns. Right now, getting access information after initialization is an extra step that the API doesn't make convenient. By returning the access set directly, the initialization step becomes more informative and useful to callers.
