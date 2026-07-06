# Add a Coordinated Status-Clearing API to the Addons Manager

## Description

Storybook's test providers accumulate status data over time — things like test pass/fail results and accessibility warnings. When the user clicks "clear all statuses" in the sidebar, the current implementation directly manipulates internal status stores rather than going through the test provider abstraction. This means the clearing logic is fragile, tightly coupled to internals, and cannot be extended by third-party or future test providers.

## Expected Behavior

- The addons manager API should expose a method that signals all registered test providers to clear their own status data.
- When called, this method should invoke each test provider's clear callback if the provider has one.
- Test providers that don't define a clear callback should be silently skipped — no error should be thrown.
- If one provider's clear callback throws an error, the method should catch it and continue clearing the remaining providers. The error must not bubble up to the caller.
- Each test provider that wants to participate should be able to declare an optional clear callback in its registration object.
- The built-in test provider should implement this clear callback to reset its component test statuses and accessibility statuses when clearing is requested.

## Why This Matters

This change makes status clearing a first-class operation of the addon API rather than a side-door manipulation of internal stores. Any test provider — built-in or third-party — can participate by declaring a clear callback. The UI can trigger clearing through a single well-defined API call without knowing the internals of each provider.
